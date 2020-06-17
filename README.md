## INSTALLATION

For compiling and using this package CUPS, libqpdf (8.3.0 or
newer), libjpeg, libpng, libtiff, freetype, fontconfig, liblcms
(liblcms2 recommended), libavahi-common, libavahi-client, libdbus,
and glib are needed. It is highly recommended, especially if
non-PDF printers are used, to have at least one of Ghostscript,
Poppler, or MuPDF.

Step 1: Clone the official cups repository.
```bash
git clone https://github.com/OpenPrinting/cups-filters.git
```

Step 2:  

___

## List of CUPS Filters

* [pdftopdf](#pdftopdf)

* bannertopdf
* commandtoescpx
* commandtopclx
* gstopdf
* gstopxl
* gstoraster
* **[imagetopdf](#imagetopdf)**
* imagetops
* imagetoraster
* mupdftoraster
* pclmtoraster
* [pdftops](#pdftops)
* [pdftoraster](#pdftoraster)
* rastertoescpx
* [rastertopclm](#rastertopclm)
* [rastertopclx](#rastertopclx)
* [rastertopdf](#rastertopdf)
* rastertops
* texttopdf
* texttops
* texttotext
* urftopdf

#pdftoippprinter

___

<h2 id="imagetopdf"> IMAGETOPDF </h2>

1. ### INTRODUCTION

    This program is "imagetopdf". "imagetopdf" is a CUPS filter which reads
    a single image file, converts it into a PDF file and outputs it to stdout.

    This program accepts the following image file format;

    gif, png, jpeg, tiff, photocd, portable-anymap, portable-bitmap,
    portable-graymap, portable-pixmap, sgi-rgb, sun-raster, xbitmap,
    xpixmap, xwindowdump

    xbitmap, xpixmap and xwindowdump images are converted into png images by
    the "convert" command. Other kinds of image file format can be supported
    if the "convert" command support them.

    Output PDF file format conforms to PDF version 1.3 specification, and
    input image is converted and contained in the output PDF file as a binary format non-compression image.

    "imagetopdf" may outputs multiple pages if the input image exceeds page
    printable area.

2. ### LICENSE

    "imagetopdf.c" is under the CUPS license. See the "COPYING" file.
    For other files, see the copyright notice and license of each file.

3. ### COMMAND LINE
    "imagetopdf" is a CUPS filter, and the command line arguments, environment variables and configuration files are in accordance with the CUPS filter
    interface.

        imagetopdf <job> <user> <title> <num-copies> <options> [<filename>]

    "imagetopdf" ignores \<job> and \<user>.

    **\<title>** is appended into the PDF dictionary as /Title.

    **\<num-copies>** specifies the number of document copies.

    **\<options>** is a CUPS option list. The detailed list of options along examples can be found [below](#imagetopdfoptions).

    **\<filename>** is an input image file name.

    When omit the **\<filename>**, "imagetopdf" reads an image file from **stdin**.

4. ### ENVIRONMENT VARIABLES

    This program refers the following environment variable;

        PPD:  PPD file name of the printer.

5. <h3 id="imagetopdfoptions"> COMMAND OPTIONS </h3>

    "imagetopdf" accepts the following CUPS standard options;

    * #### fitplot
        Compared to the PostScript-based original CUPS filters there is a
        change of defaults: The imagetopdf and imagetoraster filters print
        in "scale-to-fit" mode (image is scaled to fill one page but
        nothing of the image being cut off) by default.

        This is done to support photo printing via AirPrint. The photo
        apps on Apple's iOS devices send print jobs as JPEG images and do
        not allow to set any options like "scaling" or the page size. With
        "scale-to-fit" mode set by default, the iOS photos come out on one
        page, as expected.

        To get back to the old behavior, supply one of the options
        "nofitplot" "filplot=Off", "nofit-to-page", or "fit-to-page=Off".


    * #### mirror
    * #### PageSize
    * #### page-left, page-right, page-bottom, page-top
    * #### OutputOrder
    * #### Collate
    * #### sides
    * #### cupsEvenDuplex
    * #### position
    * #### scaling
    * #### ppi
    * #### natural-scaling
    * #### landscape
    * #### orientation-requested

6. ### KNOWN PROBLEMS

    Problem:\
        PBM and SUN raster images can not be printed.\
    Solution:\
        Due to the CUPS libcupsimage library's bug. Update the CUPS on your system.

___

<h2 id="pdftopdf"> PDFTOPDF </h2>

1. ### INTRODUCTION

    The pdftopdf filter depends on libqpdf to read and write PDF files.

    It replaces and imitates the pstops filter in the PDF-based workflow.
    A similar filter (which can serve as behavior reference)
    is called "cgpdftopdf" in OS X (not open source).

2. ### COMMAND LINE

    pdftopdf follows the usual CUPS filter calling conventions, i.e.

    pdftopdf \<job> \<user> \<title> \<num-copies> \<options> [\<filename>]

    together with the environment variables "PPD" and "CLASSIFICATION".

    When omitting \<filename>, "pdftopdf" reads a PDF file from stdin.
    Internally this will write the data to a temporary file, because
    the PDF format cannot be processed in a streaming fashion.

    \<options> are delimited by space; boolean type CUPS options can be set
    by only adding the option key, other types are provided as
    pairs of key and value, \<key>=\<value>.

3. <h3 id="pdftopdfoptions"> COMMAND OPTIONS </h3>

    "pdftopdf" processes the following standard command-line and/or PPD options:

    * #### Copies      # ppd will only override, when commandline parameter was 1
    * #### fitplot / fit-to-page / ipp-attribute-fidelity
    * #### landscape / orientation-requested
    * #### PageSize / page-size / MediaSize / media-size
    * #### page-left / page-right / page-bottom / page-top
    * #### media-top-margin / media-left-margin / media-right-margin / media-bottom-margin
    * #### Duplex / JCLDuplex / EFDuplex / JD03Duplex / sides
    * #### number-up / number-up-layout
    * #### page-border
    * #### OutputOrder / OutputBin / DefaultOutputOrder / page-delivery
    * #### page-label
    * #### page-set
    * #### page-ranges
    * #### MirrorPrint / mirror
    * #### emit-jcl
    * #### position
    * #### Collate / multiple-document-handling / sheet-collate
    * #### cupsEvenDuplex
    * #### cupsManualCopies  # via ppd

4. ### Additional (non-standard) options

    1) Booklet printing

        booklet=Off/On/Shuffle-Only

        "On" also tries to set DuplexTumble (two-sided-short-edge) and forces number-up=2

        booklet-signature=(multiple of 4, or default: -1 to use "all pages")

    2) Page autorotate

        pdftopdf automatically rotates pages to the same orientation,
        instead of (e.g. fitplot) scaling them down unrotated.
        This behavier can be controlled by

        pdfAutorotate / nopdfAutorotate

        Specifically, if a PDF file contains pages with page width greater than
        page height (a landscape page), such pages are automatically rotated
        anticlockwise by 90 degrees, unless the PPD file specifies
        "*LandscapeOrientation: Minus90". In this case, clockwise rotation is used.
        To turn off the feature on a job-by-job basis use

        lp -d <print_queue_name> -o nopdfAutorotate <document>

        On a per-queue basis use

        -o nopdfAutorotate-default

        as an option to lpadmin.

        When the 'landscape' or 'orientation-requested=4' (or =5) option of CUPS is
        given, the pdfAutorotate processing will adjust and accordingly rotate the
        non-landscape pages are rotated instead.

        Note: Some pages might end up 180 degree rotated (instead of 0 degree).
        Those should probably be rotated manually before binding the pages together.

    3) Method of flattening interactive PDF forms and annotations.

    Some PDF files (like application forms) contain interactive forms
    which the user can fill in inside a PDF viewer like evince. The filled
    in data is not integrated in each page of the PDF file but stored in
    an extra layer. Due to this the data gets lost when applying
    manipulations like scaling or N-up to the pages. To prevent the loss
    of the data pdftopdf flattens the form before doing the
    manipulations. This means the PDF will be converted into a static PDF
    file with the data being integral part of the pages.

    The same flattening is needed for annotations in PDF files.

    By default the actual flattening work is done by QPDF, as QPDF is also
    doing everything else in pdftopdf. This way no external utilities need
    to be called and so extra piping between processes and extra PDF
    interpreter runs are avoided which makes the filtering process faster.

    As we did not test the new QPDF-based form-flattening with thousands
    of PDF files yet and it has not been available to actual users yet it
    is possible that there are still some bugs. To give users a
    possibility to work around possible bugs in QPDF's form flattening, we
    have introduced an option to get back to the old flattening by the
    external tools pdftocairo or Ghostscript.

    The selection of the method is done by the "pdftopdf-form-flattening"
    option, setting it to "auto", "qpdf", "pdftocairo", "ghostscript",
    "gs", "internal" or "external":

    Per-job:           lpr -o pdftopdf-form-flattening=pdftocairo ...
    Per-queue default: lpadmin -p printer -o pdftopdf-form-flattening-default=gs
    Remove default:    lpadmin -p printer -R pdftopdf-form-flattening-default

    By default, pdftopdf uses QPDF if the option is not supplied, also the
    settings "auto" and "internal" make QPDF being used. "external"
    auto-selects from the two external utilities, trying pdftocairo at
    first and on failure Ghostscript. If the selected utility fails, the
    form stays unflattened and so the filled in data will possibly not get
    printed.

Native PDF Printer / JCL Support
--------------------------------

Note that for most modern native PDF printers JCL is not needed any
more as they are controlled via IPP. For these the PPD files get
auto-generated by the support of CUPS and cups-filters for driverless
IPP printing.

pdftopdf will emit JCL when provided with a PPD file that includes the
"*JCLToPDFInterpreter:" keyword.

This enables for hardware copy generation and device collate; e.g. with PJL:

*JCLBegin:           "<1B>%-12345X@PJL JOB<0A>"
*JCLToPDFInterpreter: "@PJL ENTER LANGUAGE = PDF <0A>"
*JCLEnd:             "<1B>%-12345X@PJL EOJ <0A><1B>%-12345X"

For each marked option, the prefixed "pdftopdfJCL<option name>" keywords
can also be used to send raw JCL strings for that option.
These keywords also include *pdftopdfJCLBegin and *pdftopdfJCLCopies,
This allows the use of the same PPD for PDF- and PS-based workflows,
as pdftopdfJCL... will not be read in the PS case.

When the PPD contains the "Copies" keyword, pdftopdf will detect the use
of PJL and has special code which adds "@PJL SET COPIES=...",
or "@PJL SET QTY=...", respectively.

Other JCL code can be injected via "*JCLOpenUI: ..." ... "*JCLCloseUI: ...".

Special PDF comments
--------------------

pdftopdf adds comments to the pdf preamble that might esp. be of use
to subsequent filters, e.g.

  % This file was generated by pdftopdf
  %%PDFTOPDFNumCopies : 1
  %%PDFTOPDFCollate : false

The "NumCopies" and "Collate" values refer to the expected device/hardware
copies, i.e. when pdftopdf's soft-copy generation did not handle this options.

Limitations
-----------

pdftopdf does not support functions that are not related to printing
features, including interactive features and document interchange features.
Many of these operators and sections are just ignored.
Some of these may be output, but those functions are not assured.

Most notable is the use of AcroForms; their content will not be printed
if any non-trivial processing by pdftopdf is involved (e.g. "fitplot").
This only occurs when a file is printed directly, e.g. by "lpr".

Usual PDF viewer applications (xpdf, evince, acroread, ghostscript, ...)
will hardcopy the form content into printable pdf operations,
when choosing to print such a document.

Known issues
------------

- Borders, esp. in the "number-up=1 fitplot=false"-case might be drawn
  at incorrect locations.

- JCL documentation is sparse.
  The imagetopdf or old pdftopdf documentation contains a tad more information.

- Missing AcroForm-content might surprise users printing PDF files directly /
  from the command-line (see the Limitations section, above).

License
-------

pdftopdf is released under the MIT license.

The required libqpdf is available under version 2.0 of the Apache License,
e.g. here: https://github.com/qpdf/qpdf


___

<h2 id="pdftops"> PDFTOPS </h2>

___

<h2 id="pdftoraster"> PDFTORASTER </h2>

___

<h2 id="rastertopclm"> RASTERTOPCLM </h2>

___

<h2 id="rastertopclx"> RASTERTOPCLX </h2>

___

<h2 id="rastertopdf"> RASTERTOPDF </h2>

