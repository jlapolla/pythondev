===============================
Python Development Docker Image
===============================

This is the Docker image I use for Python development (or, rather, the
Dockerfile I use to generate the Docker image). It provides a development
environment for the following Python versions:

- Python 2.6
- Python 2.7
- Python 3.3
- Python 3.4
- Python 3.5
- Python 3.6

Having all these Python versions available in one Docker container is handy if
you're testing with a tool like ``tox`` and you have to support a myriad of
Python versions.

Build with::

  # bash
  sudo docker build --build-arg uid=$(id -u) -t jlapolla/pythondev:multi contexts/multi

Run with::

  # bash
  sudo docker run -it --mount type=bind,src=$HOME,dst=/host --rm jlapolla/pythondev:multi

This starts the Docker container with your home directory mounted at ``/host``
within the container. Once you're in the Docker container you can do whatever
work you like in ``/host``, and your work is written to your home directory on
your local machine. Passing ``uid=$(id -u)`` at build time ensures that all file
permissions are correct, and you have a seamless experience working in the
Docker container. It's basically like spawning a subshell that has a bunch of
Python versions installed and some handy development tools.

===============
Personalization
===============

Right now, the Dockerfile is set up to build **MY** development environment.
When the image is built, it copies my personal preference files such as
``.bashrc``, ``.gitconfig``, ``.vimrc``, et cetera. You will need to edit these
files before you run ``docker build`` in order to make the image **YOUR**
development environment.

All personalization happens in the ``### Personalization`` section at the end of
the Dockerfile, so it should be pretty easy to edit.
