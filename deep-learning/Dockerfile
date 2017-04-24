FROM gcr.io/tensorflow/tensorflow:latest-gpu

# Pillow needs libjpeg by default as of 3.0.
RUN apt-get update && apt-get install -y --no-install-recommends \
        libjpeg8-dev \
	git \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN pip --no-cache-dir install scikit-learn pyreadline Pillow backports-abc backports.shutil-get-terminal-size backports.ssl-match-hostname certifi configparser cycler dask decorator Django entrypoints enum34 funcsigs functools32 h5py image ImageHash ipykernel ipython ipython-genutils ipywidgets Jinja2 jsonschema jupyter jupyter-client jupyter-console jupyter-core Keras MarkupSafe matplotlib mistune mock nbconvert nbformat networkx numpy pathlib2 pbr pexpect pickleshare prompt-toolkit protobuf ptyprocess Pygments pyparsing python-dateutil pytz PyWavelets PyYAML pyzmq qtconsole scikit-image scipy simplegeneric singledispatch six terminado Theano toolz tornado traitlets wcwidth widgetsnbextension nltk openpyxl seaborn 
RUN pip --no-cache-dir install pandas gensim
# There's a bug in Keras 2.0.0 as it depends on tensorflow (the cpu version) which gots installed and takes precedence over the gpu version hence we have to reinstall
RUN pip install --upgrade --force-reinstall tensorflow-gpu

RUN mkdir -p /notebooks/TF-examples && \
	mv /notebooks/*.ipynb /notebooks/TF-examples 
RUN mkdir -p /notebooks/Keras-examples
COPY Keras-examples/examples /notebooks/Keras-examples

WORKDIR /notebooks
CMD ["/run_jupyter.sh"]
