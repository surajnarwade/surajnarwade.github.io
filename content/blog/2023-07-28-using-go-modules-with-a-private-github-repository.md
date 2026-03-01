+++
title = "Using Go modules with a Private GitHub repository"
slug = "using-go-modules-with-a-private-github-repository"
author = "Suraj Narwade"
date = "2023-07-28T14:41:56.003Z"
category = "Blog"
+++

working with open\-source Go modules is more straightforward, you add them in Go mod files and run Go mod commands and it just works.


Recently, I came across private Go modules usage in one of my projects at work and thought of writing about it so it can help someone :)


if you have only one repo then you can do



```
export GOPRIVATE=github.com/yourorg/yourrepo

```

If you have more than one private module, you can run the following:



```
export GOPRIVATE=github.com/yourorg/*

```

If you are using the SSH key to connect to Github, you will have to add the following snippet in `~/.gitconfig`



```
[url "ssh://git@github.com/"]
    insteadOf = https://github.com/

```

once everything is set,



```
go get -u github.com/yourorg/yourrepo

```

if you want to pull a specific package, you can do,



```
go get -u github.com/yourrepo/yourorg/yourdir

```

Happy Coding !!!


