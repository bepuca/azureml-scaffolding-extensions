FROM pytorch/torchserve:0.5.3-cpu

CMD [ \
    "torchserve", "--start", \
    "--model-store","$MODEL_BASE_PATH/$MODEL_NAME/$MODEL_VERSION/", \
    "--models","$MODEL_NAME=$MODEL_NAME-$MODEL_VERSION.mar" \
]
