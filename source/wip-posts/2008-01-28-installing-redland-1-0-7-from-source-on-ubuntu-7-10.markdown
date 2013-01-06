--- 
layout: post
title: Installing redland-1.0.7 from source on Ubuntu 7.10
tags: 
- Blog
- rdf redland ruby ubuntu
status: publish
type: post
published: true
---

The Ubuntu packages for the [Redland](http://www.librdf.org) RDF libraries seem to have an issue, at least for the Ruby bindings (see [Ruby objects to cyclic dependency](https://bugs.launchpad.net/ubuntu/+source/redland-bindings/+bug/102845) about the "cyclic include detected" issue), so I installed it from source.

In case I need to do this again (or someone else does), here are the steps I took for installing Redland on Ubuntu 7.10:

1. Get the GNU MP Bignum library:

<code><pre>
sudo apt-get install libgmp3-dev
</pre></code>

2. Make or go to a scratch directory:

<code><pre>
cd ~
mkdir redland
cd redland
</pre></code>

3. Get redland 1.0.7 and unpack:

```
wget http://download.librdf .org/source/redland-1.0.7.tar.gz
tar xvfz redland-1.0.7.tar.gz
cd redland-1.0.7
```

4. Build raptor first:

```
cd raptor
./configure && make && make check && sudo make install
cd ..
```

4. Then build rasqal:


```
cd rasqal
./configure && make && make check && sudo make install
cd ..
```

_Note: If you see "Can't locate XML/DOM.pm in @INC" during make check, then try this first:_

```
sudo apt-get install libxml-dom-perl 
```

5. Build redland:

_(We are back in the main redland-1.0.7 directory)_

`./configure && make && make check && sudo make install`

6. Get and build redland-bindings:

```
cd ..
wget http://download.librdf.org/source/redland-bindings-1.0.7.1.tar.gz
tar xvfz redland-bindings-1.0.7.1.tar.gz
cd redland-bindings-1.0.7.1
./configure && make && make check && sudo make install
```

7. Build the language-specific bindings you would like:

```
cd ruby
make && make check && sudo make install
cd ..
```
