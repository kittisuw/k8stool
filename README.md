## Install kubernetes tool
### krew
``` bash
$ sudo yum install git
$ (
set -x; cd "$(mktemp -d)" &&
curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/krew.{​​​​​​​​tar.gz,yaml}​​​​​​​​" &&
tar zxvf krew.tar.gz &&
KREW=./krew-"$(uname | tr '[:upper:]' '[:lower:]')_amd64" &&
"$KREW" install --manifest=krew.yaml --archive=krew.tar.gz &&
"$KREW" update
)
$ cat >> ~/.bashrc << EOF
export PATH="\${​​​​​​​​KREW_ROOT:-\$HOME/.krew}​​​​​​​​/bin:\$PATH"
EOF
$ source ~/.bashrc
$ kubectl krew version
$ kubectl krew update
```
### kubectl-ns and kubectl-ctx
> REF: <https://github.com/ahmetb/kubectx>
``` bash
$ sudo yum install bash-completion bash-completion-extras
$ kubectl krew install ns
$ kubectl krew install ctx
$ git clone https://github.com/ahmetb/kubectx.git ~/.kubectx
$ COMPDIR=$(pkg-config --variable=completionsdir bash-completion)
$ sudo ln -sf ~/.kubectx/completion/kubens.bash $COMPDIR/kubens
$ sudo ln -sf ~/.kubectx/completion/kubectx.bash $COMPDIR/kubectx
$ cat << FOE >> ~/.bashrc
#kubectx and kubens
export PATH=~/.kubectx:\$PATH
FOE
```
### fzf
REF: <https://github.com/junegunn/fzf>
``` bash
$ git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
  | Cloning into '/home/admin/.fzf'...
  | remote: Enumerating objects: 101, done.
  | remote: Counting objects: 100% (101/101), done.
  | remote: Compressing objects: 100% (94/94), done.
  | remote: Total 101 (delta 4), reused 29 (delta 2), pack-reused 0
  | Receiving objects: 100% (101/101), 179.32 KiB | 0 bytes/s, done.
  | Resolving deltas: 100% (4/4), done.
$ ~/.fzf/install
  | Downloading bin/fzf ...
  |   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
  |                                  Dload  Upload   Total   Spent    Left  Speed
  | 100   637  100   637    0     0    966      0 --:--:-- --:--:-- --:--:--   966
  | 100 1127k  100 1127k    0     0   242k      0  0:00:04  0:00:04 --:--:--  301k
  |   - Checking fzf executable ... 0.21.1
 *| Do you want to enable fuzzy auto-completion? ([y]/n) y
 *| Do you want to enable key bindings? ([y]/n) y
  | 
  | Generate /home/admin/.fzf.bash ... OK
  | 
 *| Do you want to update your shell configuration files? ([y]/n) y
  | 
  | Update /home/admin/.bashrc:
  |   - [ -f ~/.fzf.bash ] && source ~/.fzf.bash
  |     + Added
  | 
  | Finished. Restart your shell or reload config file.
  |    source ~/.bashrc  # bash
  | 
  | Use uninstall script to remove fzf.
  | 
  | For more information, see: https://github.com/junegunn/fzf
```
### kube-ps1 
> REF: <https://github.com/jonmosco/kube-ps1>
``` bash
$ git clone --depth 1 https://github.com/jonmosco/kube-ps1.git ~/.kube-ps1
$ cat << FOE >> ~/.bashrc
# kube-ps1
[ -f ~/.kube-ps1/kube-ps1.sh ] && source ~/.kube-ps1/kube-ps1.sh
PS1='[\u@\h \W \$(kube_ps1)]\$ '
FOE
$ cp ~/.kube-ps1/kube-ps1.sh{​​​​​​​​,.org}​​​​​​​​
$ vi ~/.kube-ps1/kube-ps1.sh
  | (...)
  | # Build our prompt
  | kube_ps1() {​​​​​​​​
  |   [[ "${​​​​​​​​KUBE_PS1_ENABLED}​​​​​​​​" == "off" ]] && return
  |   [[ -z "${​​​​​​​​KUBE_PS1_CONTEXT}​​​​​​​​" ]] && [[ "${​​​​​​​​KUBE_PS1_CONTEXT_ENABLE}​​​​​​​​" == true ]] && return
  | 
  |   local KUBE_PS1
  |   local KUBE_PS1_RESET_COLOR="${​​​​​​​​_KUBE_PS1_OPEN_ESC}​​​​​​​​${​​​​​​​​_KUBE_PS1_DEFAULT_FG}​​​​​​​​${​​​​​​​​_KUBE_PS1_CLOSE_ESC}​​​​​​​​"
  | 
  |   # Background Color
  |   [[ -n "${​​​​​​​​KUBE_PS1_BG_COLOR}​​​​​​​​" ]] && KUBE_PS1+="$(_kube_ps1_color_bg ${​​​​​​​​KUBE_PS1_BG_COLOR}​​​​​​​​)"
  | 
  |   # Prefix
  |   [[ -n "${​​​​​​​​KUBE_PS1_PREFIX}​​​​​​​​" ]] && KUBE_PS1+="${​​​​​​​​KUBE_PS1_PREFIX}​​​​​​​​"
  | 
  |   # Symbol
 *|   #KUBE_PS1+="$(_kube_ps1_color_fg $KUBE_PS1_SYMBOL_COLOR)$(_kube_ps1_symbol)${​​​​​​​​KUBE_PS1_RESET_COLOR}​​​​​​​​"
  | 
 *|   #if [[ -n "${​​​​​​​​KUBE_PS1_SEPARATOR}​​​​​​​​" ]] && [[ "${​​​​​​​​KUBE_PS1_SYMBOL_ENABLE}​​​​​​​​" == true ]]; then
 *|   #  KUBE_PS1+="${​​​​​​​​KUBE_PS1_SEPARATOR}​​​​​​​​"
 *|   #fi
  | 
  |   # Context
  |   if [[ "${​​​​​​​​KUBE_PS1_CONTEXT_ENABLE}​​​​​​​​" == true ]]; then
  |     KUBE_PS1+="$(_kube_ps1_color_fg $KUBE_PS1_CTX_COLOR)${​​​​​​​​KUBE_PS1_CONTEXT}​​​​​​​​${​​​​​​​​KUBE_PS1_RESET_COLOR}​​​​​​​​"
  |   fi
  | 
  |   # Namespace
  |   if [[ "${​​​​​​​​KUBE_PS1_NS_ENABLE}​​​​​​​​" == true ]]; then
  |     if [[ -n "${​​​​​​​​KUBE_PS1_DIVIDER}​​​​​​​​" ]] && [[ "${​​​​​​​​KUBE_PS1_CONTEXT_ENABLE}​​​​​​​​" == true ]]; then
  |       KUBE_PS1+="${​​​​​​​​KUBE_PS1_DIVIDER}​​​​​​​​"
  |     fi
  |     KUBE_PS1+="$(_kube_ps1_color_fg ${​​​​​​​​KUBE_PS1_NS_COLOR}​​​​​​​​)${​​​​​​​​KUBE_PS1_NAMESPACE}​​​​​​​​${​​​​​​​​KUBE_PS1_RESET_COLOR}​​​​​​​​"
  |   fi
  | 
  |   # Suffix
  |   [[ -n "${​​​​​​​​KUBE_PS1_SUFFIX}​​​​​​​​" ]] && KUBE_PS1+="${​​​​​​​​KUBE_PS1_SUFFIX}​​​​​​​​"
  | 
  |   # Close Background color if defined
  |   [[ -n "${​​​​​​​​KUBE_PS1_BG_COLOR}​​​​​​​​" ]] && KUBE_PS1+="${​​​​​​​​_KUBE_PS1_OPEN_ESC}​​​​​​​​${​​​​​​​​_KUBE_PS1_DEFAULT_BG}​​​​​​​​${​​​​​​​​_KUBE_PS1_CLOSE_ESC}​​​​​​​​"
  | 
  |   echo "${​​​​​​​​KUBE_PS1}​​​​​​​​"
  | }​​​​​​​​
```
