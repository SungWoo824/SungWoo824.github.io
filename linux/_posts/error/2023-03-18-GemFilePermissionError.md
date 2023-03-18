---
layout: post  
title:  "Gem::FilePermissionError."
description:  
date: 2023-03-18 16:30 PM  
categories: error
---

### Gem::FilePermissionError 발생 원인 및 해결

Github Blog 를 진행하며 발생했던 오류입니다.

jekyll 을 이용해 블로그를 생성하기 위해 `bundle install`을 실행하는데 이런 오류가 발생하였습니다.

```console
$ bundle install
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/rubygems.rb:283:in 
`find_spec_for_exe': Could not find 'bundler' (2.4.4) required by your 
/Users/sungwoo/SungWoo824.github.io/Gemfile.lock. (Gem::GemNotFoundException)
```

### 발생 원인
오류메시지를 읽어 보면 ruby에 관련된 권한에 문제가 있어 발생하는 오류로 보여져 확인을 해 보았습니다.

간단히 root 권한으로 설치는 가능해 보여지지만, 보안상의 이유로 좋지 않은 방법으로 보여집니다.

확인해 보니 시스템 ruby를 이용하고 있기 떄문에 권한이 없어 Gem이 설치가 안되는 상태였습니다.

이를 해결하기 위해 루비 버전 관리를 위한 패키지인 rbenv를 이용하였습니다.

### 문제 해결

```console
brew update
brew install rbenv ruby-build
```

rbenv 설치후 현제 루비 버젼을 확인해 보면

```console
rbenv versions
* system (set by /Users/idong-uk/.rbenv/version)
```

system 루비 버전을 사용하고 있다는것을 확인할수 있습니다.

```console
 rbenv install -l
2.7.7
3.0.5
3.1.3
3.2.1
jruby-9.4.2.0
mruby-3.2.0
picoruby-3.0.0
truffleruby-22.3.1
truffleruby+graalvm-22.3.1

 rbenv install 3.2.1
```
사용 가능한 버전중 최신 버전을 설치 했습니다.

설치중 오류가 발생했습니다.
```console
BUILD FAILED (macOS 13.2.1 using ruby-build 20230309)

Inspect or clean up the working tree at /var/folders/6y/_42hh815011ghxv2cw3qm2500000gn/T/ruby-build.20230319004107.6019.3LV4Ma
Results logged to /var/folders/6y/_42hh815011ghxv2cw3qm2500000gn/T/ruby-build.20230319004107.6019.log

Last 10 log lines:
	Check ext/psych/mkmf.log for more details.
*** Fix the problems, then remove these directories and try again if you want.
```

루비를 빌드하기 위해서는 빌드 툴, 여러 라이브러리가 시스템에 설치되어 있어야 해서, 해당 명령어로 패키지들을 설치 해줘야 합니다.
설치후 ruby 도 설치해 줍니다
```console
brew install autoconf libpq zlib-ng libressl libreadline-java libyaml
rbenv install 3.2.1
rbenv global 3.2.1
```

rbenv PATH 를 추가합니다.

```console
vi ~/.zshrc
```

```console
[[ -d ~/.rbenv  ]] && \
  export PATH=${HOME}/.rbenv/bin:${PATH} && \
  eval "$(rbenv init -)"
```

```console
source ~/.zshrc
```

source 로 코드를 적용후 다시 `gem install bundler` 를 실행하면

```console
Done installing documentation for bundler after 1 seconds
1 gem installed
```
정상적으로 설치가 진행됬습니다.