# NLP summer project

## Заметки

### Что надо сделать:
- найти код DSSM
- найти датасеты к нему на русском и английском
- добавить ко входным данным шум и посмотреть, что получится

### Найдено:
- [исходная статья](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/cikm2013_DSSM_fullversion.pdf "исходная статья")
- [код по первой ссылке в гугле](https://github.com/liaha/dssm "код по первой ссылке в гугле")
- [статья, объясняющая код по первой ссылке в гугле](http://liaha.github.io/models/2016/06/21/dssm-on-tensorflow.html)
- ["введение в информационный поиск", книжка, прочитать](https://www.ozon.ru/context/detail/id/5497130/ "озон")
- [CNTK, только для винды и линуха, введение, объяснение про датасет, LSTM и ещё что-то](https://cntk.ai/pythondocs/CNTK_303_Deep_Structured_Semantic_Modeling_with_LSTM_Networks.html)
- [CNTK, только для винды и линуха, именно DSSM](https://cntk.ai/pythondocs/CNTK_202_Language_Understanding.html)
- [датасет, использующийся в статьях про CNTK, задача ATIS](https://catalog.ldc.upenn.edu/LDC95S26)
- [вот тут тоже немножко по датасетам есть, но что-то, похоже, не то, зато много](https://github.com/brmson/dataset-sts)
- [датасет по кликам с kaggle.com](https://www.kaggle.com/c/avazu-ctr-prediction)
- [код с ДАТАСЕТАМИ](https://github.com/xubaochuan/dssm)

## Проблемы на текущем этапе
### dssm
## dependency
- tensorflow(1.2.0)
### how to use (эмбеддинги)
- mkdir model
- in dir model create file 'vocab.txt'
- mkdir output
- cd preprocess
- python preprocess.py
- cd src/run
- python train.py
- python test.py
#### <b style='color:blue'>DONE</b>, всё работает, есть результат

### how to use w2v FINAL
- в data_provider есть get_w2v(filepath, sentence_length=20), который принимает тексты, а возвращает их в виде np.array w2v массивов. для этого нужен [GoogleNews-vectors-negative300.bin.gz](https://drive.google.com/file/d/0B7XkCwpI5KDYNlNUTTlSS21pQmM/edit) в папочке dataset
- в файле dssm.py надо было закоммитить self.words, чтобы использовались ВНЕШНИЕ эмбеддинги, а не инициализировались свои
- embedding_dim надо было везде поменять на 300.
- __если захочется обратно поменять на onehot_vec__, то надо в data_provider load_dataset исправить get_w2v на get_onehot_vec и раскоммитить self.words

### how to use fasttext
- всё то же самое, что с w2v, но надо скачать данные fasttext. пока не сделано

### how to use OUTPUT
- на выходе два файла: diff1.txt и test1.txt, которые получаются при помощи функций write_result и write_diff.
  - про функцию write_result:
    - сначала грузим запросы с помощью функции load_file_data() (просто считывает построчно данные и возвращает список)
    - затем грузим документы с помощью той же функции
    - затем вызываем pred(), который у нас тоже описан, он возвращает предикты по всем запросам и документам
    - в файл test1.txt пишем следующее: query[i] + doc[i] + pred[i]
  - про функцию write_diff:
    - первые три пункта такие же
    - label получаются из load_label_data(). Лейблы бывают 0  или 1
    - в файл diff1.txt пишем ТОЛЬКО ЕСЛИ pred[i] != label[i]
    - если уловие выше выполнено, то пишем query[i] + doc[i] + pred[i] + label[i]
