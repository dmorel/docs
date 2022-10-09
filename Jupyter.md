```bash
pip install jupyterthemes jupyter_contrib_nbextensions jupyter_nbextensions_configurator
jupyter contrib nbextension install --user
jupyter nbextensions_configurator enable --user
mkdir -p $(jupyter --data-dir)/nbextensions
cd $(jupyter --data-dir)/nbextensions
git clone https://github.com/lambdalisue/jupyter-vim-binding vim_binding
chmod -R go-w vim_binding
pip2 install jupyter_nbextensions_configurator
pip3 install jupyter_nbextensions_configurator
jt -t grade3 -cellw 95% -f roboto -fs 10

pip install clean_ipynb
clean_ipynb hello.ipynb
```
