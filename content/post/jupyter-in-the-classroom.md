+++
Description = "An overview of Jupyter, how I used it, and how I plan to use at my school"
Tags = ["jupyter"]
date = "2016-06-06T22:50:29-04:00"
title = "Jupyter in the classroom"

+++

Earlier this year I learned about [Jupyter](https://jupyter.org),
a web based 'notebook' editor that allows for both documentation and code.
I soon found
[Jessica Hamrick's post on rackspace](https://developer.rackspace.com/blog/deploying-jupyterhub-for-education/)
describing her usage of Jupyter for a class she taught.
This is what sparked my research into it,
and my journey to attempt to incorporate it into the
Computer Engineering Curiculum at RIT.
<!--more-->


# Background
I never liked doing programing on paper.
There is a certain benefit in being able to work out a problem without your
favorite editor,
but in the academic setting it's less of programming and more of
'can you express this idea in a very specific way?'.
I always slogged through programing assignments that needed to be handed in,
just to be told that I missed a semi-colon
or that my curly brackets didn't look right.
There have been two attempts made at RIT to allow students to submit code over
the Internet, `try` and MyCourses.
`try` has the problem of being super particular to the output of your submitted
program that even an extra space might give you a failing mark.
MyCourses was never made for programming, so submitting assignments is just a
matter of zipping up your assignment and hoping the grader has a similar
environment.


# Jupyter in the classroom
Thats where Jupyter comes in.
Notebooks are supposed to be portable, made to be shared among people.
[JupyterHub](https://github.com/jupyterhub/jupyterhub)
allows Jupyter to be run on a central server, removing setup from the end user 
and giving everyone access to the same environment.
You are guaranteed that if it runs for you, it will run for everyone else too.
There is also [nbgrader](https://github.com/jupyter/nbgrader),
which allows teachers to create assignments, distribute them to students,
and collect them.
It also has autograding features, but unlike `try` the students
have access to the tests.

I felt Jupyter was a good tool to use.
It easily integrates the documentation and code,
enabling a step by step process that is somewhat impractical in
traditional reports.
The documentation is easily written in markdown,
and the document can be downloaded as a LaTex formatted document.
This enables people to use Jupyter even if the class isn't
using it specifically.
Notebooks are also interactive, showing the results right then and there.
Teachers could use notebooks to show live examples in class,
rather than show code in a presentation and talk about it.
Jupyter also supports many languages,
well beyond the original language of Python.
In particular it supports MATLAB, a language used extensively by engineers
and in many classes.

With this information, I wanted to see if it could actually work for a class
that was not designed to use it.
My objectives were to:

1. Find out if Jupyter was flexible enough to adapt to classes not even aware
of its existence
2. Find out if it was intuitive enough for students to use
3. Explore nbgrader and other plugins to enable teachers to use
Jupyter for assignments

To test my theory on its benefits of it in the academic environment,
last semester I worked with a teacher and attempted to do every
assignment, project, and homework in the class in Jupyter.
It was a Digital Signal Processing class.
The assignment and projects were done in MATLAB,
with a few homework problems from the book.


# Setup
I was able to request a server from my department with
the specification that I needed.
I decided to run JupyterHub in a Linux container using LXD so that I could take
snapshots and restore them in the event something went wrong.
I used NGINX to proxy all web requests to the container.
I also used NGINX to enable HTTPS (Rather than do it on Jupyters end)
JupyterHub defaults to use Linux user accounts for authentication,
which was fine in this case.

It just so happened that there is a
[MATLAB kernel](https://github.com/calysto/matlab_kernel) for Jupyter.
I didn't really want to deal with licensing,
so I rewrote the assignments as I got them to work with
[Octave](https://www.gnu.org/software/octave/),
an open source alternative to MATLAB.
There also happened to be an
[Octave kernel](https://github.com/calysto/octave_kernel) which is what I used.
When I installed Octave, I also installed all of its libraries as well.

Our class also use a library produced by the book author named `spfirst`.
This library was made for MATLAB and did not work with Octave.
I ported this library to work with both,
available [here](https://github.com/DeepHorizons/spfirst).

Since my spelling is atrocious, I also decided to install a spell checker.
[Calico](https://bitbucket.org/ipre/calico) is the one I used.  
(`NOTE:` Their website is down, the following steps can be taken to install it
```
jupyter nbextension install https://bitbucket.org/ipre/calico/downloads/calico-spell-check-1.0.zip
jupyter nbextension enable calico-spell-check
```
There is also https://github.com/Calysto/notebook-extensions which hosts some
other extensions.)

Lastly, I installed pandoc and LaTeX so that the notebooks could be downloaded
as PDFs.
LaTex and all of its packages were installed to ensure compatibility.


# Results
The test was mostly successful.
Out of the two projects, I did the first 100% in Jupyter.
The second could have been done in Jupyter,
but would involve downloading, many file uploads, and playing audio,
things that Jupyter isn't good at yet.
All but one assignment was done in Jupyter.
The one that wasn't needed a specific MATLAB tool
that is not available in Octave.
Homework was practical to do in Jupyter until graphs and charts had to be drawn.

It was very nice being able to include graphs and plots so easily.
I could execute a small piece of code, explain it, then execute some more.
The document flow was unlike a traditional report where the code is in
the appendix, being explained earlier in the paper.
The reports I made did not distinguish the sections between design and results.
A teacher may not like this combination, but it is very well possible to
make this distinction, I just felt it worked better in this way.

As for my objectives:

1. Find out if Jupyter was flexible enough to adapt to classes not even aware
of its existence
    * Jupyter is definitely flexible enough to be used for nearly any class that
    has a coding requirement. I even used it for assignments in other classes
    that didnt have coding because it was easier to write in markdown than
    in LaTex.
    * Some special care must be taken to assure that any report made meets the
    departments requirements for a standard lab report.
2. Find out if it was intuitive enough for students to use
    * I believe that Jupyter is intuitive enough for the average student.
    I had some other students try it out, once they got past the initial
    questions, they were able to pick it right up.
    * Downloading files is interesting. A file that Jupyter can open must be
    downloaded using `File->Download` rather than right clicking and
    downloading. All other files are downloaded by right clicking and
    downloading. This may be unintuitive and cause some problems.
    * You cannot play sounds through a notebook. This was quite critical
    in this class. The workaround was to have the notebook generate a sound
    file, and then access it via the browser and play it.
    This is not ideal and this should be investigated to find a better solution.
3. Explore nbgrader and other plugins to enable teachers to use
Jupyter for assignments
    * Currently there is no web UI for interfacing with nbgrader,
    everything must be done via the command line.
    This is inconvenient for teachers who are not familiar with Linux.
    In my testing I made a few helper scripts but that can only go so far.
    * The autograding feature works by having a box for implementation,
    then several tests. If the tests fail, then the points are not awarded.
    But the tests are always visible to the students.
    This disallows usages where you would not want teh student to see the tests
    line in a simple question you would see from other web based quizes.


# Next Steps
The next steps is to move my single user instance and allow the department
to use it.
This means using a different authentication method other than the user accounts.
Our school uses Shibboleth as its primary authentication system.
Apache has a `mod_shib` plugin that would allow a near drop in.
There is then a
[JupyterHub authenticator](https://github.com/cwaldbieser/jhub_remote_user_authenticator)
than can use the data to handle the rest.
(More can be read [here](https://github.com/jupyterhub/jupyterhub/issues/332))

Another point is security and files.
No student should be able to see or access another students files.
This can be accomplished by using Docker and containers.
Docker can be used to spin up the instance of Jupyter for each student.
Their files can then be linked using NFS or Samba.
This also helps with distribution, as one server can only handle about 30
students from what I have read.

The other two steps are student adoption and professor adoption.
For this to be successful, the end goal is to have students use this to
make their lives easier.
If the students do not use it, then there is no point.
I believe that some students will gravitate twards using this.
It is simple enough to get started and has a lot of potential for helping.
Once students begin to use it,
professors could then start to use it for their class.
It could start with them giving in class examples, making notebooks available
for the students to look at.
Once professors are comfortable with Jupyter, they may then begin using it
for assignment.

The targeted classes in my department are the Digital Signal Processing,
Assembly, and Applied Programming (which uses C).
Octave is already supported by Jupyer and there is a kernel for running
C/C++.
At the time there is no assembly interpreter for ARM
which is what the class uses.
I am current writing an interpreter to be used in the class with hopes of
releasing a beta in a few weeks.
