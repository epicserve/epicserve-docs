# Combine Multiple PDFs into a Single PDF

First install Ghostscript. You can install Ghostscript using brew with `brew install ghostscript`.

After Ghostscript is installed run the following...

    gs -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -sOutputFile=merged.pdf file1.pdf file2.pdf