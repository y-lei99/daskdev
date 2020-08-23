FROM registry.cn-beijing.aliyuncs.com/k8s_test/geonotebook:geolab-v2

USER root

RUN apt-get update \
    && apt-get install -yq --no-install-recommends graphviz git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

USER $NB_UID

RUN conda install --yes \
    -c conda-forge \
    python-blosc \
    cytoolz \
    dask \
    lz4 \
    nomkl \
    numpy \
    pandas \
    ipywidgets \
    dask-labextension \
    python-graphviz \
    && jupyter labextension install @jupyter-widgets/jupyterlab-manager dask-labextension \
    && conda clean -tipsy \
    && jupyter lab clean \
    && jlpm cache clean \
    && npm cache clean --force \
    && find /opt/conda/ -type f,l -name '*.a' -delete \
    && find /opt/conda/ -type f,l -name '*.pyc' -delete \
    && find /opt/conda/ -type f,l -name '*.js.map' -delete \
    && find /opt/conda/lib/python*/site-packages/bokeh/server/static -type f,l -name '*.js' -not -name '*.min.js' -delete \
    && rm -rf /opt/conda/pkgs

USER root

# Create the /opt/app directory, and assert that Jupyter's NB_UID/NB_GID values
# haven't changed.
RUN mkdir /opt/app \
    && if [ "$NB_UID" != "1000" ] || [ "$NB_GID" != "100" ]; then \
    echo "Jupyter's NB_UID/NB_GID changed, need to update the Dockerfile"; \
    exit 1; \
    fi

# Copy over the example as NB_USER. Unfortuantely we can't use $NB_UID/$NB_GID
# in the `--chown` statement, so we need to hardcode these values.
COPY examples/ /home/$NB_UID/examples
COPY prepare.sh /usr/bin/prepare.sh
RUN chmod +x /usr/bin/prepare.sh

USER $NB_UID

ENTRYPOINT ["/jupyter.sh"]
#CMD ["start.sh", "jupyter", "lab"]
