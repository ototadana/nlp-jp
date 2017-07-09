# nlp-jp + fastText

[nlp-jp](https://hub.docker.com/r/ototadana/nlp-jp/) + [fastText](https://github.com/facebookresearch/fastText)

### 利用例
```bash
$ docker run --rm -v $PWD:/app -w /app ototadana/fasttext mecab -Owakati example.txt > example-wakati.txt
$ docker run --rm -v $PWD:/app -w /app ototadana/fasttext fasttext skipgram -input example-wakati.txt -output model
```
