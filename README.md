# GitLeaks


[![Build Status](https://travis-ci.org/zricethezav/gitleaks.svg?branch=master)](https://travis-ci.org/zricethezav/gitleaks)
[![godoc](https://godoc.org/github.com/zricethezav/gitleaks?status.svg)](http://godoc.org/github.com/zricethezav/gitleaks)
[![GolangCI](https://golangci.com/badges/github.com/zricethezav/gitleaks.svg)](https://golangci.com)

## Check git repos for secrets and keys

### Features

* Search all commits on all branches in topological order
* Regex/Entropy checks

#### Installing

```bash
go get -u github.com/zricethezav/gitleaks
```

#### Usage and Explanation

![Alt Text](https://github.com/zricethezav/gifs/blob/master/gitleaks.gif)

```bash
./gitleaks {git url}
```

This example will clone the target `{git url}` and run a diff on all commits. A report will be outputted to `{repo_name}_leaks.json`
Gitleaks scans all lines of all commits and checks if there are any regular expression matches. The regexs are defined in `main.go`. For example if a line in a commit diff like `AWS_KEY='AKAI...'` exists then the value after the assignment operator will be checked for entropy. If the value is above a certain entropy threshold then we assume that the line contains a key/secret. Work largely based on  [https://people.eecs.berkeley.edu/~rohanpadhye/files/key_leaks-msr15.pdf](https://people.eecs.berkeley.edu/~rohanpadhye/files/key_leaks-msr15.pdf) and https://github.com/dxa4481/truffleHog.

##### gitLeaks User
```bash
./gitleaks -u {user git url}
```
##### gitLeaks Org
```bash
./gitleaks -o {org git url}
```

#### Help
```
usage: gitleaks [options] [git url]


Options:
	-c 			Concurrency factor (potential number of git files open)
	-u 		 	Git user url
	-r 			Git repo url
	-o 			Git organization url
	-s 			Strict mode uses stopwords in checks.go
	-e 			Base64 entropy cutoff, default is 70
	-x 			Hex entropy cutoff, default is 40
	-h --help 		Display this message
```
NOTE: your mileage may vary so if you aren't getting the results you expected try tweaking the entropy cutoffs and stopwords. Entropy cutoff for base64 alphabets seemed to give good results around 70 and hex alphabets seemed to give good results around 40. Entropy is calculated using [Shannon entropy](http://www.bearcave.com/misl/misl_tech/wavelets/compression/shannon.html).


### If you find a valid leak in a repo
Please read the [Github article on removing sensitive data from a repository](https://help.github.com/articles/removing-sensitive-data-from-a-repository/) to remove the sensitive information from your history.

### Run me with docker

Simply run `docker run --rm --name=gitleaks raphaelareya/gitleaks https://github.com/zricethezav/gitleaks`

Or build the image yourself to get the latest version :

```
docker build -t gitleaks .
docker run --rm --name=gitleaks gitleaks https://github.com/zricethezav/gitleaks
```

#### TODO

* Specify a target branch
* Support for custom regex
* Filter regex
* ~~Modify entropy cutoff~~
