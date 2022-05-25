FROM pytorch/torchserve:0.6.0-cpu

CMD [ \
    "torchserve", "--start", \
    "--model-store","$MODEL_BASE_PATH/", \
    "--models","model=$MODEL_NAME-$MODEL_VERSION.mar" \
]
