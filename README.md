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
### how to use w2v
- в файле preprocess.py есть generate_vocab(), который ОТКРЫВАЕТ файл vec.txt, НО НЕ СОЗДАЁТ ЕГО.
- в файле dssm.py есть флаг fine_tune, заменить на True, тогда будет v2w вместо эмбеддингов
- в dssm.py есть метод load_w2v(), который считывает из vec.txt
### w2v troubles
- проблемы с созданием vec.txt. есть ощущение, что перепутаны файлы vec.txt и w2v_corpus.txt, потому что w2v_corpus.txt создаётся и никак не используется. <b style='color:blue'>DONE</b>
- lhs shape= [1952,200] rhs shape= [2278,200]. <b style='color:red'>¯\_(ツ)_/¯</b>

#### w2v troubles, разбор чужого кода
- файл vec.txt, который нигде не создаётся, но оттуда считывается в разных местах. какова была задумка автора? надо воссоздать этот файл.
  - во-первых, везде, где с vec.txt считываются данные, пишется: size = int(первая_строка_распарсенная_через_" "[0]), dim = int(первая_строка_распарсенная_через_" "[1]). это значит, что в первой строке файла vec.txt должны быть размер (какой размер? видимо, длина словаря) и размерность (опять же, что за размерность, у автора написано "the dim of the fusion of multi word2vec model") файла, записанные через пробел. видимо, для удобства.
  - dim должна быть равна expectDim, а expectDim у автора 200
- ВЫВОД: надо воссоздать vec.txt. каким образом:
  - преобразуем файл w2v_corpus.txt (или какой-то другой, но этот нигде не используется, значит может быть это оно), который состоит из просто подряд записанных предложений, в файл w2v, причём в его первой строке должны быть dim и size, а dim должно быть 200 (ну или около того, тогда исправить флаг).

#### w2v troubles, разбор чужого кода, part 2
- создала файл vec.txt, функция load_w2v() теперь работает ОК
- НО теперь где-то вектор не того размера. lhs shape= [1952,200] rhs shape= [2278,200]
