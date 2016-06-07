+++
date = "2016-02-20T10:35:39-05:00"
draft = true
title = "Building Kaldi on Windows: Part 1"

+++
This is a multi part series about building Kaldi on Windows with Microsoft Visual Studio 2015


# Speech Recognition
Like others, I have always been interested in adding speech recognition to my projects.
When I first heard about [CMU Sphinx](http://cmusphinx.sourceforge.net/)
or [Julius](http://julius.osdn.jp/en_index.php)
I quickly jumped to learn them and integrate them into my project.
It mostly lead to a fruitless result, mostly because I was young and ignorant,
but partly because the tools were just not the most accurate.
<!--more-->
My last attempt was to build a bot for a Mumble server,
sitting in the lounge listening for keywords so that it could report back
some useful information.
For example, if you asked it "Whats the weather like?"
It would respond with
"The current weather is partly cloudy with a temperature of 56 degrees".
When I started I tried to use Julius, but this was fruitless for reasons which I cannot remember.
The next attempt was to use Sphinx.
This proved to be more useful, there was already [a python library](https://github.com/cmusphinx/pocketsphinx-python)
And they provide [models](https://sourceforge.net/projects/cmusphinx/files/Acoustic%20and%20Language%20Models/US%20English%20Generic%20Acoustic%20Model/)
So that you can just hit the ground running.

After tinkering around, fixing bugs, and figuring out how to get it all set up,
it did in fact work.
The problem?
It couldn't get my speech correct.
It would get a close approximate, but never enough to determined what I said.
The solution to this was to use a keyword file,
A list of phrases and a probability level to limit what Sphinx would recognize
(called Out-of-Grammar rejection).
This was fine for a small project with a limited domain of possible inputs,
like days of the week or specific phrases for certain actions,
but this would never be suitable for anything larger.


# Kaldi
And thats when I heard about [Kaldi](http://kaldi-asr.org/).
Kaldi is the 'Next Gen' of speech recognition.
Developed in 2011 as a research project, it uses current modern technology and algorithms
to achieve speech recognition that's leaps and bounds better than the current alternatives.
Kaldi is also designed to be loosely coupled,
making it easy to switch in newer technology or algorithms as they become available
([which has already happened](http://kaldi-asr.org/doc/online_programs.html))

So why hasn't everyone switched over to Kaldi?
Because it nearly requires a PHD to use it.
It is very much still a research project,
and the [documentation](http://kaldi-asr.org/doc/) isnt quite up to par for the average person.
There are efforts to revise the documentation to enable ease of use,
but it is still far from being done.

Luckly a lot of the heavy lifting has been done.
Tanel Alum has made a gstreamer plugin that takes audio from an input file and pipes it into kaldi.
It is not yet in the kaldi source, and therefor must be compiled separetely.
He has already made a server that accepts websocket requests.
If you just want to use that, you get get it
[here](https://github.com/alumae/kaldi-gstreamer-server),
or get it as a docker container
[here](https://github.com/jcsilva/docker-kaldi-gstreamer-server).


# Why compile on Windows?
Because thats what I use for my everyday development.


## Prereqs
I used:

* Windows 7 x64
* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* [git](https://git-scm.com/download/win)
* perl

You will also need to apply patches.
I use [MSYS2](https://msys2.github.io/), but it is not required.
Look up [Patch for Windows](http://gnuwin32.sourceforge.net/packages/patch.htm).
In the examples I use `wget` and other command line tools,
but you can do the actions manually.

Kaldi had some instructions for building on windows located [In the windows folder](https://github.com/kaldi-asr/kaldi/blob/master/windows/INSTALL.md)
from which much of this is derived.
Read that for the most up to date install steps.

It is good to note here that we will be building a 64 bit version of Kaldi
and of all the tools.
You may try for the 32 bit version, but you will have to pick the correct download
for the tools (namely OpenBlas, Win32-int32).
I have not tried this, so YMMV.

## Getting the stuff
The first step is to clone the repo

```
git clone https://github.com/kaldi-asr/kaldi.git kaldi
```

cd into the tools directory and checkout the windows version of
[OpenFst](http://openfst.org).
Kaldi *can* be compile with 1.4.x, but it doesn't seem to work on windows.

```
cd kaldi/tools
git clone https://github.com/jtrmal/openfstwin-1.3.4.git openfst
```

You need to get pthread-win32 as well.
Download
http://downloads.sourceforge.net/project/pthreads4w/pthreads-w32-2-9-1-release.zip
and unzip it into a folder named `pthreads`.

```
wget http://downloads.sourceforge.net/project/pthreads4w/pthreads-w32-2-9-1-release.zip
mkdir pthreads
cd pthreads
unzip ../pthreads-w32-2-9-1-release.zip
```

Lastly, you need a BLAS package.
I recommend OpenBlas as it is free,
but Kaldi supports MLK as well.
If you do use OpenBlas, you need the minwg dll as well.

```
cd ..
wget http://sourceforge.net/projects/openblas/files/v0.2.14/OpenBLAS-v0.2.14-Win64-int32.zip
wget http://sourceforge.net/projects/openblas/files/v0.2.14/mingw64_dll.zip
unzip OpenBLAS-v0.2.14-Win64-int32.zip
unzip mingw64_dll.zip
```

## Patches
Patches need to be applied to OpenFst and pthreads

```
cd openfst
patch -p1 < ../extras/openfstwin-1.3.4.patch
```

pthreads need a patch because of MSVS 2015 will throw an error if `timespec` is redefined.

```
cd ../pthreads
patch -p1 < ../extras/pthread-2.9.1.patch
```

## Building
First step is to build OpenFst.
Open the solution in the latest MSVCnn folder and compile it under `Release` and `x64`.

After it has successfully compiled, we need to modify the `variable.props` file
under `kaldi/windows`.
Modify the `OPENBLAS`, `PTHREADW`, `OPENFST`, and `OPENFSTLIB` values.
Use the absolute path and make sure to keep the folder the same.
Use the current values as a reference.

You also need to delete the `kaldiwin.props` file and reaname `kaldiwin_openblas.props`
to `kaldiwin.props`.

The Kaldi solution is ready to be built.
Use the perl script `generate_solution.pl` with the appropriate arguments.
We will be using `--enable-openblas` and `vs2015` as the version

```
perl generate_solution.pl --vsver vs2015 --enable-openblas
```

This will create a folder named `kaldiwin_vs2015_OPENBLAS`
in the root of the kaldi directory.
Open the solution file in it and change the project to `Release` and `x64`.
Then simply go to `Build > Build Solution`

At this point, the build is going to take a while.
Go make a bowl of popcorn and watch a movie.
And if everything went alright, you'll have a fully working compiled version
of Kaldi.
A few projects will have failed, in particular the `online` projects.
These projects rely on [PortAudio](http://www.portaudio.com/)
