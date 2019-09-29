# Alternative splicing
```sh
docker pull ecell/epdp

```

Below are examples of how to get started with Enthought Deployment Manager on your machine:
```sh
# Make sure edm is installed and available
$ edm --version

# Install package of your choice, e.g. NumPy
# (Creates a default Python environment)
$ edm install numpy

# Create a shell with the default environment activated
$ edm shell
```
How to install in a different environment:
```sh
$ edm install -e test-old-numpy "numpy < 1.10"

# Create a shell with the test-old-numpy environment activated
$ edm shell -e test-old-numpy
```
For more information or to get help:
```sh
# See a list of available commands and help topics
$ edm help

# Get help on a specific command, e.g. install
$ edm help install

# Get help on a topic, e.g. configuration
$ edm help configuration
```
install software
```sh


```
