# Pocket2Linkding

Simple tool to migrate from [Mozilla Pocket](https://getpocket.com/home) to
[linkding](https://linkding.link/).

## Purpose

Like many others, I needed to find a new home for my bookmarks when Mozilla announced on
May 22, 2025 that they are [shutting down Pocket](https://support.mozilla.org/en-US/kb/future-of-pocket)
as of July 8, 2025.  I wanted something self-hosted.  After looking at many different options
(see [awesome-selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted?tab=readme-ov-file#bookmarks-and-link-sharing)),
I decided to switch to [linkding](https://linkding.link/) ([GitHub](https://github.com/sissbruecker/linkding)).
I put together a quick Python-based migration tool to convert from the
[Pocket export format](https://support.mozilla.org/en-US/kb/exporting-your-pocket-list) to
linkding's import format while preserving the original time the link was added to Pocket
and any tags that you have set.

Note that while Pocket is shutting down on July 8, 2025, Mozilla will keep the
[export](https://support.mozilla.org/en-US/kb/exporting-your-pocket-list) service up until
October 8, 2025.

## Usage

While there are many ways to run Python scripts, this should be enough to get you going:

## Create, Download, and Unzip the Mozilla Pocket Export

* Use the [Pocket export](https://support.mozilla.org/en-US/kb/exporting-your-pocket-list) to
    initiate an export.
* Mozilla will send you a link to download the CSV export once it's ready.  Download the
    `pocket.zip` file and unzip it on your local system.: `unzip pocket.zip`
    * The `pocket.zip` file contains one or more files with names like `part_000000.csv`.
    * If you have more than 10,000 bookmmarks, there will be multiple files with 10K bookmarks
        in each (e.g., `part_000000.csv`, `part_000001.csv`, etc.)

## Run the Migration Tool

Use the migration tool to convert each `part_######.csv` file into a `part_######.html` bookmark
file that can be imported into linkding.

### Using Python [uv](https://docs.astral.sh/uv/)

* Install uv if you don't have it already
    * See [Installing uv](https://docs.astral.sh/uv/getting-started/installation/) for details,
        but they recommend `curl -LsSf https://astral.sh/uv/install.sh | sh`.
    * Note that many people have legitimate security concerns about using curl to pipe commands
        into the shell in this manner.  See the "Installing uv" link above for alternatives.
* View the help info: `uv run Pocket2Linkding.py -h`
* Run the migration: `uv run Pocket2Linkding -d .`

### Using a Python Virtual Environment

I'm skipping the normal virtual environment activation step here, but feel free to
[include that if you want](https://docs.python.org/3/library/venv.html#how-venvs-work).

* Create a Python Virtual Environment: `python3 -m venv venv`
* Install dependencies: `./venv/bin/pip3 install attrs unidecode`
* View the help info: `./venv/bin/python Pocket2Linkding.py -h`
* Run the migration: `./venv/bin/python Pocket2Linkding.py -d .`

Or, for Windows:

* Create a Python Virtual Environment: `py -3 -m venv venv`
* Install dependencies: ` .\venv\Scripts\pip.exe install attrs unidecode`
* View the help info: `.\venv\Scripts\python.exe .\Pocket2Linkding.py -h`
* Run the migration: `.\venv\Scripts\python.exe .\Pocket2Linkding.py -d .`

### Using Docker

You can run this command to execute the process inside Docker, and not worry about polluting your environment or running scary scripts. This assumes you've downloaded the script itself to `$HOME/Downloads`, and have put the `part_######.csv` files in `$Home/Downloads/pocket`; the output will go into the latter directory.

```
docker run -it --rm -w /in -v "$HOME"/Downloads/Pocket2Linkding.py:/app/Pocket2Linkding.py -v $HOME/Downloads/pocket:/in astral/uv:bookworm-slim uv run /app/Pocket2Linkding.py --pocket_export_dir /in
```

## Import Into linkding

* [Install linkding](https://linkding.link/installation/) if you haven't already.  I recommend
    using [Docker](https://www.docker.com/) and using the `latest-plus` image to get the
    built-in ability to archive pages using
    [SingleFile](https://github.com/gildas-lormeau/single-file-cli).
* Open your instance of linkding.
* Go to Settings -> General.
* Near the bottom of the page you will see an "Import" section.
* Click the "Browse" button and select a .html file.
* Click "Upload".
* Repeat for each file if you have more than 10K bookmarks saved in Pocket.
