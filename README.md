# Eagle Eye

Simple scripts for recognizing faces, and identifying people, across multiple video files. 

## Basic usage

Install dependencies:

```bash
pip install -r requirements
```

Put all your video files in a directory (i.e. /videos -- it can include subfolders, etc.). Create another directory for saving face images (i.e. /faces) to.

```bash
mkdir /videos /faces
#put all your video files (subfolders are fine) in /videos directory
```

Extract face images from video files:

```bash
./extract-faces --directory /videos --output /faces
```

(Depending on your system, and number of videos, this will take awhile to run.)

Group extracted images by face:

```bash
./group-faces --directory /faces --output faces.csv
```

The /faces directory now contains images of all images recognized from the videos, and faces.csv is a listing of each face image file and the unique face pictured.

### OS/X

On OS/X, you will need to install GNU xargs, as the version of xargs built into OS/X is lacking some needed command-line options.  The recommended way to do this is via the Homebrew package `findutils`:
```
brew install findutils
```

## Advanced usage

It’s made up of a few scripts. Here’s more detailed usage details:

#### extract-faces

```
usage: extract-faces [-h] --directory DIRECTORY --output OUTPUT
                     [--processes PROCESSES] [--interval INTERVAL]
                     [--margin MARGIN] [--model {cnn,hog}]
                     [--tolerance TOLERANCE] [--upsample UPSAMPLE]
                     [--maxfaces MAXFACES] [--preview]

recursively scan directory, processing video files, recognizing faces and
exporting them to image files in an output directory

optional arguments:
  -h, --help            show this help message and exit
  --directory DIRECTORY
                        path to directory containing videos (directory will be
                        scanned recursively, so sub-folders are fine)
  --output OUTPUT       path to write face images to (creates directory if not
                        present)
  --processes PROCESSES
                        number of videos to process in parallel, DEFAULT: 1
  --interval INTERVAL   milliseconds to skip between processing frames (i.e.
                        1000 will process one frame for every 1 second of
                        video, 0 will process all frames). DEFAULT: 1000
  --margin MARGIN       adds margin around face images equal to percent of
                        width and length (DEFAULT: 0.40, i.e. for a 100px by
                        100px face, a 140px by 140px image will be extracted)
  --model {cnn,hog}     which face detection model to use. "hog" is less
                        accurate but faster on CPUs. “cnn” is a more accurate
                        deep-learning model which is GPU/CUDA accelerated (if
                        available). The default is "hog"
  --tolerance TOLERANCE
                        how much distance between faces to consider it a match
                        (lower numbers require greater similarity, DEFAULT:
                        0.9)
  --upsample UPSAMPLE   How many times to upsample the image looking for
                        faces. Higher numbers find smaller faces. (DEFAULT: 1)
  --maxfaces MAXFACES   maximum images number of the same face to export.
                        (DEFAULT: 10, higher numbers will export more images
                        of the same person as it scans more frames)
  --preview             show video preview while processing, DEFAULT: false

For each video file, a subdirectory is created in the output path, named the
checksum of the video path (i.e. directory/test.mp4 ->
output/17f53613b2b25b29d192d5b11bc77459). For each video file, when a face is
recognized, it is compared to other recognized faces. Each unique person is
assigned an id. Face images are exported as PNG files named as
output/<checksum of video path>/<id of person>_<ms timestamp of video>.png
(i.e. "output/17f53613b2b25b29d192d5b11bc77459/31_5333.png")
```

#### group-faces

```
usage: group-faces [-h] --directory DIRECTORY --output OUTPUT
                   [--model {cnn,hog}] [--tolerance TOLERANCE]

compare face images in directory, group by similarity (i.e. same person), and
output CSV file of groupings

optional arguments:
  -h, --help            show this help message and exit
  --directory DIRECTORY
                        path to directory face images (directory will be
                        scanned recursively, so sub-folders are fine)
  --output OUTPUT       path to write CSV file to
  --model {cnn,hog}     which face detection model to use. "hog" is less
                        accurate but faster on CPUs. “cnn” is a more accurate
                        deep-learning model which is GPU/CUDA accelerated (if
                        available). The default is "hog"
  --tolerance TOLERANCE
                        how much distance between faces to consider it a match
                        (lower numbers require greater similarity, DEFAULT:
                        0.9)

All image files in directory are scanned for faces. When a face is recognized,
it is compared to other recognized faces. Each unique face is assigned an id.
A CSV file is written with "<image path>,<face id>" for each image file.
```

#### extract-faces-from-video

```
usage: extract-faces-from-video [-h] --video VIDEO --output OUTPUT
                                [--interval INTERVAL] [--margin MARGIN]
                                [--model {cnn,hog}] [--tolerance TOLERANCE]
                                [--upsample UPSAMPLE] [--maxfaces MAXFACES]
                                [--preview]

Scan video file, frame by frame, recognize faces and export them to image
files in an output directory

optional arguments:
  -h, --help            show this help message and exit
  --video VIDEO         path to video file (uses OpenCV and supports most
                        video formats)
  --output OUTPUT       path to directory for exporting face images (creates
                        directory if not present)
  --interval INTERVAL   milliseconds to skip between processing frames (i.e.
                        1000 will process one frame for every 1 second of
                        video, 0 will process all frames). DEFAULT: 1000
  --margin MARGIN       adds margin around face images equal to percent of
                        width and length (DEFAULT: 0.40, i.e. for a 100px by
                        100px face, a 140px by 140px image will be extracted)
  --model {cnn,hog}     which face detection model to use. "hog" is less
                        accurate but faster on CPUs. “cnn” is a more accurate
                        deep-learning model which is GPU/CUDA accelerated (if
                        available). The default is "hog"
  --tolerance TOLERANCE
                        how much distance between faces to consider it a match
                        (lower numbers require greater similarity, DEFAULT:
                        0.9)
  --upsample UPSAMPLE   How many times to upsample the image looking for
                        faces. Higher numbers find smaller faces. (DEFAULT: 1)
  --maxfaces MAXFACES   maximum images number of the same face to export.
                        (DEFAULT: 10, higher numbers will export more images
                        of the same person as it scans more frames)
  --preview             show video preview while processing, DEFAULT: false

When a face is recognized, it is compared to other recognized faces. Each
unique person is assigned an id. Face images are exported as PNG files named
as output/<id of person>_<ms timestamp of video>.png (i.e.
"output/31_5333.png")
```

## TODO:

I created this project quickly. Apologies in advance for issues, bugginess, ugly output, or clunky implementations. Please feel free to fork or contact me if you would like to join the repo.

I’ve only run this on my Linux system. I’m guessing it will work on any system with the appropriate Python dependencies install, and xargs.

1. Create docker image so project can be run in containerized environment
2. Script to generate boilerplate HTML for exploring faces and videos -- something like generating a folder of HTML files:
  1. HTML file for each individual, showcasing each face image for the individual
  2. Clicking a face image loads an embedded player of the referenced video at time face appears
  3. Index page showing all individuals, with search abilities
3. Integrations with third-party services (i.e. using Facebook API, check if any faces match photos of Facebook friends).
