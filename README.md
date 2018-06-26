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

#### w2v troubles, разбор чужого кода, part 3
- нужно было в качестве исходника для vec.txt использовать vocab.txt
- всё работает и тренится, <b style='color:blue'>DONE</b>

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
    
### how to use fasttext
- всё то же самое, что с w2v, но я создала файлик fasttex.txt, c ним всё работает OK
- <b style='color:blue'>DONE</b>

## СПИСОК ВОПРОСОВ ПО КОДУ
- в файле __dssm/src/nnModel/dssm.py__ в строке 20, есть флаг fine_tune. если там поставить True, то в строке 53 того же файла будет вызываться функция load_w2v. первый вопрос: я не понимаю, что происходит если флаг False (строка 56): self.words = tf.Variable(tf.random_uniform([FLAGS.vocab_size, FLAGS.embedding_dim]), dtype=tf.float32, name='words'). ну, то есть, я вижу, что это равномерное распределение, но почему так, зачем, я не понимаю.
- в файле __dssm/src/preprocess/preprocess.py__ есть функция generate_vocab_small(stcA, stcB), строка 46. она делает понятные вещи: составляет словарь всех слов и сохраняет его в файл vocab.txt. именно этот vocab.txt я преобразовала к w2v виду (и сохранила как vec.txt) и считала с помощью load_w2v из первого пункта, все размерности сошлись, из чего я сделала вывод, что поняла замысел автора.
- в файле __dssm/src/dataProvider/data_provider.py__ есть функция load_vocab() (строка 77) и глобальная переменная vocab = load_vocab(vocab.txt) (строка 93). там же есть функция get_onehot_vec (строка 40), которая считывает слова с переменной vocab. эта функция заменяет слова на их номера из списка и добавляет нулей до минимальной длины предложения (20 слов) или наоборот, делит на две части. 
- в файле __dssm/src/dataProvider/data_provider.py__ есть функция load_dataset(), которая грузит все датасеты и гоняет их через вышеописанный get_onehot_vec().

### Вывод
Таким образом, я понимаю, что тренится нейросеть не на словах, а на их номерах в файле (логично, потому что с картинками то же самое, трёхканальный цвет пикселя заменяется на 1, ..., nclasses). И я НЕ понимаю, на что влияет преобразование vocab.txt в vec.txt

### Новые проблемы с w2v
- на данный момент файлы конвертятся в w2v правильно, но изменился входной вектор: ValueError: Cannot feed value of shape (600, 20, 200) for Tensor 'input/QueryData:0', which has shape '(?, 20)'
- где это исправлять пока неясно, в keras просто есть запись input shape. где он тут, не могу найти
