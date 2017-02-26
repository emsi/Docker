FROM gcr.io/tensorflow/tensorflow:latest-gpu

# Pillow needs libjpeg by default as of 3.0.
RUN apt-get update && apt-get install -y --no-install-recommends \
        libjpeg8-dev \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    mkdir -p /notebooks/storage-flash && mkdir -p /notebooks/storage-flash

RUN pip install scikit-learn pyreadline Pillow backports-abc backports.shutil-get-terminal-size backports.ssl-match-hostname certifi configparser cycler dask decorator Django entrypoints enum34 funcsigs functools32 h5py image ImageHash ipykernel ipython ipython-genutils ipywidgets Jinja2 jsonschema jupyter jupyter-client jupyter-console jupyter-core Keras MarkupSafe matplotlib mistune mock nbconvert nbformat networkx notebook numpy pathlib2 pbr pexpect pickleshare prompt-toolkit protobuf ptyprocess Pygments pyparsing python-dateutil pytz PyWavelets PyYAML pyzmq qtconsole scikit-image scipy simplegeneric singledispatch six tensorflow terminado Theano toolz tornado traitlets wcwidth widgetsnbextension 

RUN rm -rf /notebooks/*

VOLUME /notebooks/storage-flash
VOLUME /notebooks/storage-hdd

WORKDIR /notebooks
CMD ["/run_jupyter.sh"]
