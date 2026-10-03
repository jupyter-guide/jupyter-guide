# Export your Notebooks to HTML and PDF

Sometimes a reader needs a static copy of a notebook: a reviewer who will not run code, a supervisor who reads on a tablet, or a journal that wants a supplementary PDF. This chapter shows how to make that copy with [nbconvert](https://nbconvert.readthedocs.io/en/latest/usage.html) and how to check it before you send it.

This is part of Rule 9 of the [Ten Simple Rules](https://github.com/jupyter-guide/ten-rules-jupyter), *Design your notebooks to be read, run, and explored*: a static copy is for reading. It is also a snapshot. Keep the `.ipynb` file (and its environment, see [Deploy your notebooks](deployment.md)) as the reproducible source, and send the HTML or PDF next to it, not instead of it.

The example notebook [Rule9_Export_Example.ipynb](../notebooks/Rule9_Export_Example.ipynb) contains the four things that most often go wrong: an equation, a plot, a wide table and a long line of code. Use it to try every step below.

## 1. Decide which outputs end up in the file

nbconvert does **not** run your notebook unless you ask it to. By default it copies the outputs that are saved in the `.ipynb` file, even if they are stale or were produced out of order.

You have two options:

* **Export the saved outputs.** In Jupyter, run *Kernel > Restart & Run All*, check that the execution counts go 1, 2, 3, … from top to bottom, save, then export.
* **Re-execute while exporting.** Add `--execute`. nbconvert starts a fresh kernel, runs every cell from top to bottom and exports the result. Your `.ipynb` file is not changed.

  ```bash
  jupyter nbconvert --to html --execute Rule9_Export_Example.ipynb
  ```

  The export stops at the first cell that raises an error. Add `--allow-errors` if the error should appear in the output instead. For a long-running notebook you can set a per-cell limit in seconds, e.g. `--ExecutePreprocessor.timeout=600`.

Re-executing is the stronger check: if the notebook only works because of a cell you ran by hand and later deleted, `--execute` fails. If you want to keep the re-executed outputs in the notebook itself, write them back with:

```bash
jupyter nbconvert --to notebook --execute --inplace Rule9_Export_Example.ipynb
```

See [Executing notebooks](https://nbconvert.readthedocs.io/en/latest/execute_api.html) in the nbconvert documentation for more options.

## 2. Choose a format

| Format | Command | Needs | Good for |
|---|---|---|---|
| HTML | `jupyter nbconvert --to html` | nbconvert only | Sharing by email or on a web page. Wide tables and long lines scroll instead of being cut off. |
| PDF via LaTeX | `jupyter nbconvert --to pdf` | [Pandoc and a TeX distribution with XeLaTeX](https://nbconvert.readthedocs.io/en/latest/install.html#installing-tex) | Documents that should look like a LaTeX paper. |
| PDF via a browser (WebPDF) | `jupyter nbconvert --to webpdf` | `pip install "nbconvert[webpdf]"` and Chromium | A PDF that looks like the notebook in the browser, without installing TeX. |

Install nbconvert, with the extra dependencies for WebPDF, using:

```bash
pip install "nbconvert[webpdf]"
```

WebPDF prints the page with Chromium through [Playwright](https://playwright.dev/python/docs/browsers). If no Chromium is found, either run `playwright install chromium` once or add `--allow-chromium-download` to the nbconvert command.

> **Windows:** with nbconvert 7.17 the command-line `--to webpdf` can fail with `NotImplementedError` ([jupyter/nbconvert#2287](https://github.com/jupyter/nbconvert/issues/2287)). Until that is fixed, run it under WSL, or export to HTML, open the HTML file in Chrome or Edge, and use *Print > Save as PDF*. That uses the same browser engine.

Try the HTML and WebPDF exports on the example notebook:

```bash
jupyter nbconvert --to html Rule9_Export_Example.ipynb
jupyter nbconvert --to webpdf Rule9_Export_Example.ipynb
```

This writes `Rule9_Export_Example.html` and `Rule9_Export_Example.pdf` next to the notebook. Add `--output-dir exports` to put them somewhere else, or `--no-input` to leave out the code cells for a report-style document.

## 3. Check the result

Open the exported file and compare it with the notebook. Do not only look at the first page.

* **Plots:** every figure is there. Images created by code are embedded in the file. Images that a Markdown cell loads from a local path stay links to that file, so they break when the HTML is sent on its own; add `--embed-images` to embed them in HTML and WebPDF exports.
* **Equations:** they are typeset, not shown as raw `$...$` source. HTML and WebPDF exports load MathJax from a CDN, so equations in an HTML file opened offline may stay raw.
* **Wide tables:** the last column is still there. In the example's WebPDF export (Letter paper), the 12-column table loses `sample_08` to `sample_12` at the right margin. They are not hidden: they are gone from the PDF. The transposed table (`summary.T`) right below it fits.
* **Long code lines:** the end of each line is still there. In the same WebPDF export, the long line is cut off before `at the margin`. The version split over several lines, right below it, is complete. Keeping lines under about 80 characters, as [PEP 8](https://peps.python.org/pep-0008/#maximum-line-length) suggests, avoids the problem.
* **Page breaks:** a figure can land on the next page, away from the code that made it, as the histogram does in the example. There is no nbconvert option to keep a cell together. If the break hides context, add a sentence of Markdown before the figure, shorten the cells above it, or send the HTML instead.

A quick way to find content that was cut off is to search the PDF for a word you know is at the end of the line or table, as `sample_12` and `at the margin` are in the example.

## 4. Know where your notebook goes

Outputs can contain more than you expect: rows of raw data, file paths with user names, tokens printed while debugging. Before you send or convert a notebook, decide who may see it.

* **On your own machine:** nbconvert from the command line, as in this chapter, and Jupyter's *File > Save and Export Notebook As* menu when Jupyter runs locally. Your notebook is not uploaded. (WebPDF may download Chromium once, and the HTML file loads MathJax from a CDN when it is opened.) On a shared JupyterHub, the same menu runs on the hub's server.
* **Inside the browser tab:** printing the HTML export to PDF, and some web converters that do the conversion in the page itself (for example [ipynbtopdf.xyz](https://ipynbtopdf.xyz/), maintained by the author of this section), keep the file on your computer. You can check this for any web tool in the browser's developer tools: the *Network* tab should show no upload of the notebook.
* **Upload services:** many online converters send the whole `.ipynb` file, outputs included, to their server. Read their privacy policy before using them for unpublished or sensitive work.

To share a notebook without any outputs, clear them in a **copy** of the file (the command overwrites the file it is given):

```bash
jupyter nbconvert --clear-output Rule9_Export_Example_copy.ipynb
```

## Further reading

* [nbconvert usage](https://nbconvert.readthedocs.io/en/latest/usage.html): all export formats and options
* [nbconvert installation](https://nbconvert.readthedocs.io/en/latest/install.html): Pandoc, TeX and Chromium requirements
* [Executing notebooks](https://nbconvert.readthedocs.io/en/latest/execute_api.html): `--execute`, timeouts and error handling
