# Tiny Java Image Presentation

A presentatin about how to make an production-ready Java (`> 9`) Container as small as possible.

## Install

For a easy installation use `pip`: `pip install hovercraft`

## Usage

To host the html-presentation just run

```bash
hovercraft presentation.rst
```

and go to http://localhost:8000

## Create PDF

To create a pdf of the presentation [decktape](https://github.com/astefanutti/decktape) can be used.
Results are mostly flanky -> Needs more love on the css.

```bash
decktape -s 1720x1080 impress http://localhost:8000 presentation.pdf
```

or docker execution

```bash
docker run --rm -t --network host -v $(pwd):/slides astefanutti/decktape -s 1720x1080 impress http://localhost:8000 presentation.pdf
```
