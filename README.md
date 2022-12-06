# EAF Sunburst
This repository provides the radial space-filling visualization for the [Emacs Application Framework](https://github.com/emacs-eaf/emacs-application-framework).

### Install 
1. Install [EAF](https://github.com/emacs-eaf/emacs-application-framework#install) 
2. Install eaf-sunburst: switch to directory emacs-application-framework, execute command `./install-eaf.py sunburst`
3. Then add below code in your emacs config:


```Elisp
(add-to-list 'load-path "~/.emacs.d/site-lisp/emacs-application-framework/")
(require 'eaf)
(require 'eaf-sunburst)
```

### Usage
`eaf-open-sunburst` will popup radial space-filling visualization, it's useful to analyze data from Emacs, such as, profiler-report, start-time etc.
