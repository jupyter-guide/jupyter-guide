# Simple Guidelines for Reproducible Research with Jupyter Notebook

## Tell a Story
Like any good story, a notebook should have a
* Beginning
  * Introduce the topic and the aims of the notebook
* Middle
  * Explain the steps of the workflow
  * Use markdown to split notebook into sections
* End
  * Interpret the results
  * What trends do tables, plots, or figures show?

Write notebook for an audience
* New users may need instructions how to run a notebook
* Educational materials need background and step by step explanations

## Capture Entire Workflow
* Show how you got from initial data to final results
* Avoid manual steps, use a notebook for data preparation
* Split workflow into steps, e.g., data preparation, data analysis, data visualization
* Show intermediate results so users can follow the dataflow

## Avoid Copy & Paste
* Split common functions into separate files and import them into notebooks

## Remove Clutter
* Use markdown to organize a notebook into sections
* Split long notebooks into a series of notebooks
  * Keep a top level notebook with links to the individual notebooks
* Avoid long cells
  * Split text and code into cells
  * One cell -> one paragraph or one task (e.g., create a plot)
  * Modularize code by defining functions or classes
  * Reuse code by importing functions or classes
  * Put low-level documentation in code comments

## Make it Reproducible
* Specify version numbers of dependencies
* Always specify the source/location of the data
* Keep a copy of the raw data if possible
* Make copies of data in a notebook to avoid corrupting datasets when running cells out of order
* Specify random number seeds
* Before saving a notebook, rerun notebook to ensure linear execution order

## Use Version Control
* Keep your notebooks under version control, e.g., GitHub
* Describe content of repository in README files
* Specify a license to encourage and enable use by others
* Structure your repository
  * See for [example](http://drivendata.github.io/cookiecutter-data-science)

## Share It!
* Use open source projects as your dependencies
* Add a liberal open source license (e.g., MIT, Apache 2) to your repository
* Use Nbviewer to provide static views of your executed notebook
  * [https://nbviewer.jupyter.org/](https://nbviewer.jupyter.org/)
* Use Binder to provide a zero-install environment to run your notebooks in the cloud
  * [https://mybinder.org/](https://mybinder.org/)		
* Create a Docker image of your environment
  * [https://docs.docker.com/](https://docs.docker.com/)
