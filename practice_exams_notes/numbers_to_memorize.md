SQS:
    - visibility timeout = default 30sec, range 0 to 43,200 (12 hours)
    - delay seconds = default 0, max 15min
    - long polling - 0 < waitTime <= 20sec
    - message size between 1 byte and 256 KB
    - max nr messages to receive = 10

Kinesis data stream:
    - messages visible = default 24h, max 365days