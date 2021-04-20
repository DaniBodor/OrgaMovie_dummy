# OrgaMovie Macro Information

This FiJi/ImageJ macro takes any number 4D (xyzt) \*.nd2 image files of organoids and creates color-coded (for depth) time-lapse movies (as below).

<img src="https://user-images.githubusercontent.com/14219087/114186287-f4473580-9946-11eb-99b2-1f3d73b78a69.png">

The macro will not run on recent versions of ImageJ. The most recent version of ImageJ that this has been stably tested on is 1.49b. Also, the macro currently only works from the workstation (DED-KOPS-001) in the Hubrecht Lab (2nd floor student room). If either of these is limiting for you, please talk to me to figure out a solution.

I have noticed that many people have their own installation of FiJi on their account. However, there is also a common installation of FiJi on the D-drive (strictly speaking FiJi is not installed, but just exists on the disk). I have installed this plugin-set **only on the common FiJi installation**. I recommend you delete your own insance of FiJi if you are not using it for anything else and always work from the common one, which is located under *D:\FiJi.app*, and create a shortcut for *imagej-win64.exe* on your desktop or taskbar.

The code is a fully automated adaptation of a macro previously created by Bas Ponsioen and  René M Overmeer (I think), first published in _[Targeting mutant RAS in patient-derived colorectal cancer organoids by combinatorial drug screening](https://elifesciences.org/articles/18489)_, eLife 2016;5:e18489 doi: 10.7554/eLife.18489 (I think). Original macro version 2016_03_24 was used as a basis.

# Running the OrgaMovie Macro on the Workstation (DED-KOPS-001)

1) Check that previous user has collected all their data, otherwise store/rename the _Output_Movies_ folder.
2) Delete old files from D:\ANALYSIS DUMP. This is not essential but avoids confusion with previous analyses.
    - Delete all folders called _Queue Exp \[\#\]_.
    - Delete previous output movies from _Output_Movies_ folder, or delete/rename the entire folder.
      - MAKE SURE PREVIOUS USER HAS COLLECTED THEIR DATA BEFORE DELETING!
    - Delete all _\*.txt_ files.
3) Start FiJi.
4) Start OrgaMovie macro by hitting F11, or go to _Plugins > OrgaMovies > OrgaMovie Start
5) Input your favorite settings (see below).
6) Choose input folder where your raw data (\*.nd2 files) are located.
    - Please work from local or external hard disks and NOT from the server. 
8) Wait overnight (rough time estimate: ~14h for 33 movies totalling ~200 GB).
9) Collect your output data from _D:\ANALYSIS DUMP\Final_Movies_.
10) Check that your movies are ok and then delete them from the D-drive.


# Macro Settings

## Main Settings Dialog
<img src="https://user-images.githubusercontent.com/14219087/115407951-c12b5e80-a1f0-11eb-9efc-500a5c805bc6.PNG" width="388" height="452">

#### General Settings
- Input filetype: currently the only option is '.nd2'.
- Channel number: set the channel to use in terms of channel order (so N<sup>th</sup> channel) 
    - Can be ignored if single-channel (i.e. single-color) data is used.
    - Because false colors are used to signify depth, it is unclear how to implement multi-channel depth in this macro. Talk to me if you are interested in this to see if we can figure something out.
- Time interval: set the interval (in minutes) between consecutive frames.
- Experiment name: Used for output file naming. Set a prefix for all output files, which is then combined with the file naming setting below to create unique filenames for each movie.
    - Default is your windows account + the current date in yymmdd format.
#### Movie Output Settings
- Output format: Choose whether output videos should be in between \*.avi or \*.tif or both. Tifs are easier to use for downstream analysis in ImageJ but require significantly more diskspace.
- Frame rate: The frame rate of the output movie (for \*.avi). Set how many seconds each frame stays in view when playing the movie.
- Output naming: What to use after the prefix (set above) to name individual output movies. Options are:
  - _linear_ = number movies consecutively from 1-N.
  - _filename_ = use the entire original filename (minus the extension).
  - _file index_ = use the original filename until the first underscore ( \_ ). Often filenames are numbered by the microsope software and this number is repeated after the underscore. E.g., the output resulting from _Point0004_Seq0004.nd2_, will be named \[experiment_name\]\_Point0004.avi.
#### Automation Settings (on/off)
- Drift correction - Uses _[MultiStackReg](http://bigwww.epfl.ch/thevenaz/stackreg/)_ plugin (default in FiJi) to correct drift and shaking in movies.
    - If unchecked: the organoid will move across the frame as happened during filming. As a knock-on effect, this will require a larger crop-area (see next setting) leading to larger output file size.
    - Note that the drift correction can lead to movies where it appears that a blacked out region is 'wiping' across your movie. This is in fact the organoid moving out of the field of view.
- Auto-cropping: Detects portion of frame (XY) that is visited by the organoid in any Z or T and crops around this.
    - If multiple organoid regions are found, cropping occurs around the largest region only. 
    - If unchecked: the entire frame is used, leading to (unnecessarily) large file sizes and more cluttered movies. 
    - See default automation settings for more details.
- Auto-contrasting: Automatically detects a good set of intensity values to use for contrasting (green and blue in original manual version of the macro). These are based on a lowest threshold values that exclude most organoid pixels and a highest threshold value that will include a small proportion of pixels.
    - If unchecked: dimmest and brightest pixel values are used, which tends to not give great contrast but also no pixels are overexposed or lost as background.
    - Contrasting cannot be easily adjusted in the resulting output, as RGB images/movies are produced.
    - See default automation settings for more details on thresholding.
.
- Last timepoint detection: Finds the last timepoint where an organoid is still visible within the frame. This is based on the coefficient of variation (mean/stdev) of all pixel values in the frame. The last timepoint considered is the first timepoint found where this coefficient is detected. 
    - If unchecked: all frames of the movie are included in the output, which will lead to (unnecessarily) large file sizes.
    - See default automation settings for more details.
- Change default settings: If this is checked, another dialog will be opened after this to set default automation settings.
    - If few movies turn out imperfect, try running those manually (press F10) rather than changing the settings for all movies.
    - If many movies turn out weird, perhaps changing default parameters can help. See below for details on these.

## Automation Settings Dialog
<img src="https://user-images.githubusercontent.com/14219087/114186945-b7c80980-9947-11eb-85ce-f4b3b27e777a.png" width="322" height="294">
If few movies turn out imperfect, try running those manually (press F10) rather than changing the settings for all movies. If many movies turn out weird, perhaps changing default parameters can help.

#### Auto-crop Settings
- Minimum organoid size: the minimum organoid size (in μm<sup>2</sup>) detected to crop around.
    - If no organoid of this size or larger is found, then the entire frame is used.
- Boundary around organoid: the number of pixels around the extreme edges of the organoid included in the cropped region.
#### Contrast Automation Settings
- Minimum threshold method: Choose between the ImageJ default threshold methods to detect brightness of background pixels.
- Maximum threshold method: Choose between the ImageJ default threshold methods to automate maximum brightness detection.
- Brightest-point factor: Fold-change between the brightest point detected above and what is actually used as the output brightest point
    - Lower values create brighter movies, but also will also include more overexposed pixels. Probably between 0.85-1.15 is ideal.
- Gamma factor (copied from original macro). Applies a [gamma correction](https://en.wikipedia.org/wiki/Gamma_correction) on the output images. 
    - The original macro stated "brings low and high intensities together", but I don't fully understand what a gamma correction does.
- Multiply factor (copied from original macro): I don't know what this setting does, but it was present in the original macro.
    - The original macro stated "for depth coded channel", but it is unclear to me what this setting changes.
#### Time-crop Settings
- CoV cutoff: Cut-off value for coefficient of variation (mean/stdev) for detecting last time-point. Higher values will include more fuzzy/empty frames.
- Minimum length: Minimum length of movie in case CoV cut-off is reached previously. This is mainly included as a workaround for movies that reach the CoV from frame 1, which leads to a crash.
