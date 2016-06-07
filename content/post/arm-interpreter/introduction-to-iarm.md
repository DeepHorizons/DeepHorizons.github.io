+++
Description = "An"
Tags = ["jupyter", "arm"]
date = "2016-06-06T22:50:29-04:00"
title = "Introduction to the ARM interpreter"

+++

Earlier this year I learned about [Jupyter](https://jupyter.org),
a web based 'notebook' editor that allows for both documentation and code.
It seemed like a neat idea, but I just glazed over it.
It wasn't until I read
[Jessica Hamrick's post on rackspace](https://developer.rackspace.com/blog/deploying-jupyterhub-for-education/)
that I began to see the potential for such a tool.
<!--more-->

# Background
I never liked doing programing on paper.
There is a certain benefit in being able to work out a problem without your
favorite editor,
but in the academic setting it's less of programming and more of
'can you express this idea in a very specific way?'.
I always slogged through programing assignments that needed to be handed in,
just to be told that I missed a semi-colon
or that my curly brackets aren't right.
There have been attempts made at my school to allow students to submit code
over the internet but the tool is either super particular to the output
of your submitted program, or you simply zip up the code and hope the grader
has a similar environment to you.

## Jupyter in the classroom
Thats where Jupyter comes in.
Notebooks are supposed to be portable, made to be shared among people.
[JupyterHub](https://github.com/jupyterhub/jupyterhub)
allows Jupyter to be run on a central server,
giving everyone access to the same environment.
You are guaranteed that if it runs for you, it will run for everyone else too.
There is also [nbgrader](https://github.com/jupyter/nbgrader),
which allows teachers to create assignments, distribute them to students,
and collect them.
It also has autograding features, but unlike the other tools the students
have access to the tests.

To test my theory on its benefits of it in the academic environment,
I worked with a teacher and attempted to do every
assignment in the class in Jupyter.
I requested a server from our department,
Installed Jupyter and the appropriate software and away I went.
It was a Digial Signal Processing class with assignments and projects.
The assignment and projects were done in MATLAB,
with a few problems from the book.
It just so happened that there is a
[MATLAB kernel](https://github.com/calysto/matlab_kernel) for Jupyter.
I didn't really want to deal with licensing,
so I rewrote the assignment to work with
[Octave](https://www.gnu.org/software/octave/),
an open source alternative to MATLAB.
There also happened to be an
[Octave kernel](https://github.com/calysto/octave_kernel) which is what I used.

## Results
The test was mostly successful with some hidden benefits.
Out of the two assignments, I did the first 100% in Jupyter.
The second could have been done in Jupyter,
but would involve downloading many file transfers and playing audio,
something that Jupyter isn't good at yet.
All but one assignment was done in Jupyter.
The one that wasn't needed a specific MATLAB tool
that is not available in Octave.
Homework was practical until graphs and charts were needed.

One extra benefit I looked over was the ability to download the assignment
in a $ \LaTeX $ style formatted PDF.
This is particularly useful for classes that wont be using Jupyter
to do assignments, but students still want to use it.