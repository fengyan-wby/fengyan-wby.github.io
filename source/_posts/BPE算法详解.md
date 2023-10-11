---
title: BPE算法详解
date: 2023-02-21 11:36:21
categories: 算法
tags: ["BPE", "分词"]
---

在NLP模型中，输入通常是一个句子，例如`"I went to New York last week"`，一句话中包含很多单词(token)。传统的做法是将这些单词以空格进行分隔，例如`['i', 'went', 'to', 'New', 'York', 'last', 'week']`。然而这种做法存在很多问题，例如模型无法通过`old, older, oldest`之间的关系学到`smart, smarter, smartest`之间的关系。如果我们能使用将一个 token 分成多个 subtokens ，上面的问题就能很好的解决。本文将详述目前比较常用的subtokens算法——BPE（Byte-Pair Encoding）。
<!-- more -->

## BPE(Byte Pair Encoding)

算法流程如下：

1. 设定最大subwords个数V
2. 将所有单词拆分为单个字符，并在最后添加一个停止符`</w>`，同时标记出该单词出现的次数。例如，`"low"`这个单词出现了 5 次，那么它将会被处理为`{'l o w </w>': 5}`
3. 统计每一个连续字节对的出现频率，选择最高频者合并成新的subword
4. 重复第3步直到达到第1步设定的subwords词表大小或下一个最高频的字节对出现频率为1

例如：

```json
{'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w e s t </w>': 6, 'w i d e s t </w>': 3}
```

出现最频繁的字节对是`e`和`s`，共出现了 6+3=9 次，因此将它们合并

```json
{'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w es t </w>': 6, 'w i d es t </w>': 3}
```

出现最频繁的字节对是`es`和`t`，共出现了 6+3=9 次，因此将它们合并

```json
{'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w est </w>': 6, 'w i d est </w>': 3}
```

出现最频繁的字节对是`est`和`</w>`，共出现了 6+3=9 次，因此将它们合并

```json
{'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w est</w>': 6, 'w i d est</w>': 3}
```

出现最频繁的字节对是`l`和`o`，共出现了 5+2=7 次，因此将它们合并

```json
{'lo w </w>': 5, 'lo w e r </w>': 2, 'n e w est</w>': 6, 'w i d est</w>': 3}
```

出现最频繁的字节对是`lo`和`w`，共出现了 5+2=7 次，因此将它们合并

```json
{'low </w>': 5, 'low e r </w>': 2, 'n e w est</w>': 6, 'w i d est</w>': 3}
```

...... 继续迭代直到达到预设的subwords词表大小或下一个最高频的字节对出现频率为1。这样我们就得到了更加合适的词表，这个词表可能会出现一些不是单词的组合，但是其本身有意义的一种形式。

停止符`</w>`的意义在于表示subword是词后缀。举例来说：`st`不加`</w>`可以出现在词首，如`st ar`；加了`</w>`表明改字词位于词尾，如`wide st</w>`，二者意义截然不同。

```python
# BPE词表构建代码实现
import re, collections

def get_vocab(filename):
    vocab = collections.defaultdict(int)
    with open(filename, 'r', encoding='utf-8') as fhand:
        for line in fhand:
            words = line.strip().split()
            for word in words:
                vocab[' '.join(list(word)) + ' </w>'] += 1
    return vocab

def get_stats(vocab):
    pairs = collections.defaultdict(int)
    for word, freq in vocab.items():
        symbols = word.split()
        for i in range(len(symbols)-1):
            pairs[symbols[i],symbols[i+1]] += freq
    return pairs

def merge_vocab(pair, v_in):
    v_out = {}
    bigram = re.escape(' '.join(pair))
    p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
    for word in v_in:
        w_out = p.sub(''.join(pair), word)
        v_out[w_out] = v_in[word]
    return v_out

def get_tokens(vocab):
    tokens = collections.defaultdict(int)
    for word, freq in vocab.items():
        word_tokens = word.split()
        for token in word_tokens:
            tokens[token] += freq
    return tokens

vocab = {'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w e s t </w>': 6, 'w i d e s t </w>': 3}

# Get free book from Gutenberg
# wget http://www.gutenberg.org/cache/epub/16457/pg16457.txt
# vocab = get_vocab('pg16457.txt')

print('==========')
print('Tokens Before BPE')
tokens = get_tokens(vocab)
print('Tokens: {}'.format(tokens))
print('Number of tokens: {}'.format(len(tokens)))
print('==========')

num_merges = 5
for i in range(num_merges):
    pairs = get_stats(vocab)
    if not pairs:
        break
    best = max(pairs, key=pairs.get)
    vocab = merge_vocab(best, vocab)
    print('Iter: {}'.format(i))
    print('Best pair: {}'.format(best))
    tokens = get_tokens(vocab)
    print('Tokens: {}'.format(tokens))
    print('Number of tokens: {}'.format(len(tokens)))
    print('==========')

# 输出如下
==========
Tokens Before BPE
Tokens: defaultdict(<class 'int'>, {'l': 7, 'o': 7, 'w': 16, '</w>': 16, 'e': 17, 'r': 2, 'n': 6, 's': 9, 't': 9, 'i': 3, 'd': 3})
Number of tokens: 11
==========
Iter: 0
Best pair: ('e', 's')
Tokens: defaultdict(<class 'int'>, {'l': 7, 'o': 7, 'w': 16, '</w>': 16, 'e': 8, 'r': 2, 'n': 6, 'es': 9, 't': 9, 'i': 3, 'd': 3})
Number of tokens: 11
==========
Iter: 1
Best pair: ('es', 't')
Tokens: defaultdict(<class 'int'>, {'l': 7, 'o': 7, 'w': 16, '</w>': 16, 'e': 8, 'r': 2, 'n': 6, 'est': 9, 'i': 3, 'd': 3})
Number of tokens: 10
==========
Iter: 2
Best pair: ('est', '</w>')
Tokens: defaultdict(<class 'int'>, {'l': 7, 'o': 7, 'w': 16, '</w>': 7, 'e': 8, 'r': 2, 'n': 6, 'est</w>': 9, 'i': 3, 'd': 3})
Number of tokens: 10
==========
Iter: 3
Best pair: ('l', 'o')
Tokens: defaultdict(<class 'int'>, {'lo': 7, 'w': 16, '</w>': 7, 'e': 8, 'r': 2, 'n': 6, 'est</w>': 9, 'i': 3, 'd': 3})
Number of tokens: 9
==========
Iter: 4
Best pair: ('lo', 'w')
Tokens: defaultdict(<class 'int'>, {'low': 7, '</w>': 7, 'e': 8, 'r': 2, 'n': 6, 'w': 9, 'est</w>': 9, 'i': 3, 'd': 3})
Number of tokens: 9
==========
```

### 编码和解码

***编码***

在之前的算法中，我们已经得到了 subword 的词表，对该词表按照字符个数由多到少排序。编码时，对于每个单词，遍历排好序的子词词表寻找是否有 token 是当前单词的子字符串，如果有，则该 token 是表示单词的 tokens 之一。

我们从最长的 token 迭代到最短的 token，尝试将每个单词中的子字符串替换为 token。 最终，我们将迭代所有 tokens，并将所有子字符串替换为 tokens。 如果仍然有子字符串没被替换但所有 token 都已迭代完毕，则将剩余的子词替换为特殊 token，如`<unk>`。

例如

```python
# 给定单词序列
["the</w>", "highest</w>", "mountain</w>"]

# 排好序的subword表
# 长度 6         5           4        4         4       4          2
["errrr</w>", "tain</w>", "moun", "est</w>", "high", "the</w>", "a</w>"]

# 迭代结果
"the</w>" -> ["the</w>"]
"highest</w>" -> ["high", "est</w>"]
"mountain</w>" -> ["moun", "tain</w>"]
```

***解码***
将所有的 tokens 拼在一起即可，例如

```python
# 编码序列
["the</w>", "high", "est</w>", "moun", "tain</w>"]

# 解码序列
"the</w> highest</w> mountain</w>"
```

```python
# 在构建词表代码中加入编码和解码过程的代码
import re, collections

def get_vocab(filename):
    vocab = collections.defaultdict(int)
    with open(filename, 'r', encoding='utf-8') as fhand:
        for line in fhand:
            words = line.strip().split()
            for word in words:
                vocab[' '.join(list(word)) + ' </w>'] += 1

    return vocab

def get_stats(vocab):
    pairs = collections.defaultdict(int)
    for word, freq in vocab.items():
        symbols = word.split()
        for i in range(len(symbols)-1):
            pairs[symbols[i],symbols[i+1]] += freq
    return pairs

def merge_vocab(pair, v_in):
    v_out = {}
    bigram = re.escape(' '.join(pair))
    p = re.compile(r'(?<!\S)' + bigram + r'(?!\S)')
    for word in v_in:
        w_out = p.sub(''.join(pair), word)
        v_out[w_out] = v_in[word]
    return v_out

def get_tokens_from_vocab(vocab):
    tokens_frequencies = collections.defaultdict(int)
    vocab_tokenization = {}
    for word, freq in vocab.items():
        word_tokens = word.split()
        for token in word_tokens:
            tokens_frequencies[token] += freq
        vocab_tokenization[''.join(word_tokens)] = word_tokens
    return tokens_frequencies, vocab_tokenization

def measure_token_length(token):
    if token[-4:] == '</w>':
        return len(token[:-4]) + 1
    else:
        return len(token)

def tokenize_word(string, sorted_tokens, unknown_token='</u>'):
    
    if string == '':
        return []
    if sorted_tokens == []:
        return [unknown_token]

    string_tokens = []
    for i in range(len(sorted_tokens)):
        token = sorted_tokens[i]
        token_reg = re.escape(token.replace('.', '[.]'))

        matched_positions = [(m.start(0), m.end(0)) for m in re.finditer(token_reg, string)]
        if len(matched_positions) == 0:
            continue
        substring_end_positions = [matched_position[0] for matched_position in matched_positions]

        substring_start_position = 0
        for substring_end_position in substring_end_positions:
            substring = string[substring_start_position:substring_end_position]
            string_tokens += tokenize_word(string=substring, sorted_tokens=sorted_tokens[i+1:], unknown_token=unknown_token)
            string_tokens += [token]
            substring_start_position = substring_end_position + len(token)
        remaining_substring = string[substring_start_position:]
        string_tokens += tokenize_word(string=remaining_substring, sorted_tokens=sorted_tokens[i+1:], unknown_token=unknown_token)
        break
    return string_tokens

# vocab = {'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w e s t </w>': 6, 'w i d e s t </w>': 3}

vocab = get_vocab('pg16457.txt')

print('==========')
print('Tokens Before BPE')
tokens_frequencies, vocab_tokenization = get_tokens_from_vocab(vocab)
print('All tokens: {}'.format(tokens_frequencies.keys()))
print('Number of tokens: {}'.format(len(tokens_frequencies.keys())))
print('==========')

num_merges = 10000
for i in range(num_merges):
    pairs = get_stats(vocab)
    if not pairs:
        break
    best = max(pairs, key=pairs.get)
    vocab = merge_vocab(best, vocab)
    print('Iter: {}'.format(i))
    print('Best pair: {}'.format(best))
    tokens_frequencies, vocab_tokenization = get_tokens_from_vocab(vocab)
    print('All tokens: {}'.format(tokens_frequencies.keys()))
    print('Number of tokens: {}'.format(len(tokens_frequencies.keys())))
    print('==========')

# Let's check how tokenization will be for a known word
word_given_known = 'mountains</w>'
word_given_unknown = 'Ilikeeatingapples!</w>'

sorted_tokens_tuple = sorted(tokens_frequencies.items(), key=lambda item: (measure_token_length(item[0]), item[1]), reverse=True)
sorted_tokens = [token for (token, freq) in sorted_tokens_tuple]

print(sorted_tokens)

word_given = word_given_known

print('Tokenizing word: {}...'.format(word_given))
if word_given in vocab_tokenization:
    print('Tokenization of the known word:')
    print(vocab_tokenization[word_given])
    print('Tokenization treating the known word as unknown:')
    print(tokenize_word(string=word_given, sorted_tokens=sorted_tokens, unknown_token='</u>'))
else:
    print('Tokenizating of the unknown word:')
    print(tokenize_word(string=word_given, sorted_tokens=sorted_tokens, unknown_token='</u>'))

word_given = word_given_unknown 

print('Tokenizing word: {}...'.format(word_given))
if word_given in vocab_tokenization:
    print('Tokenization of the known word:')
    print(vocab_tokenization[word_given])
    print('Tokenization treating the known word as unknown:')
    print(tokenize_word(string=word_given, sorted_tokens=sorted_tokens, unknown_token='</u>'))
else:
    print('Tokenizating of the unknown word:')
    print(tokenize_word(string=word_given, sorted_tokens=sorted_tokens, unknown_token='</u>'))

# 输出如下
Tokenizing word: mountains</w>...
Tokenization of the known word:
['mountains</w>']
Tokenization treating the known word as unknown:
['mountains</w>']
Tokenizing word: Ilikeeatingapples!</w>...
Tokenizating of the unknown word:
['I', 'like', 'ea', 'ting', 'app', 'l', 'es!</w>']
```

## GPT1中的BPE

GPT中的BPE代码实现在编码部分和上述的有些不同，这里展示GPT1的实现：

```python
import re
import ftfy
import json
import spacy

from tqdm import tqdm

def get_pairs(word):
    """
    Return set of symbol pairs in a word.
    word is represented as tuple of symbols (symbols being variable-length strings)
    """
    pairs = set()
    prev_char = word[0]
    for char in word[1:]:
        pairs.add((prev_char, char))
        prev_char = char
    return pairs

def text_standardize(text):
    """
    fixes some issues the spacy tokenizer had on books corpus
    also does some whitespace standardization
    """
    text = text.replace('—', '-')
    text = text.replace('–', '-')
    text = text.replace('―', '-')
    text = text.replace('…', '...')
    text = text.replace('´', "'")
    text = re.sub('''(-+|~+|!+|"+|;+|\?+|\++|,+|\)+|\(+|\\+|\/+|\*+|\[+|\]+|}+|{+|\|+|_+)''', r' \1 ', text)
    text = re.sub('\s*\n\s*', ' \n ', text)
    text = re.sub('[^\S\n]+', ' ', text)
    return text.strip()

class TextEncoder(object):
    """
    mostly a wrapper for a public python bpe tokenizer
    """

    def __init__(self, encoder_path, bpe_path):
        self.nlp = spacy.load('en', disable=['parser', 'tagger', 'ner', 'textcat'])
        self.encoder = json.load(open(encoder_path))
        self.decoder = {v:k for k,v in self.encoder.items()}
        merges = open(bpe_path).read().split('\n')[1:-1]
        merges = [tuple(merge.split()) for merge in merges]
        self.bpe_ranks = dict(zip(merges, range(len(merges))))
        self.cache = {}

    def bpe(self, token):
        word = tuple(token[:-1]) + ( token[-1] + '</w>',)
        if token in self.cache:
            return self.cache[token]
        pairs = get_pairs(word)

        if not pairs:
            return token+'</w>'

        while True:
            bigram = min(pairs, key = lambda pair: self.bpe_ranks.get(pair, float('inf')))
            if bigram not in self.bpe_ranks:
                break
            first, second = bigram
            new_word = []
            i = 0
            while i < len(word):
                try:
                    j = word.index(first, i)
                    new_word.extend(word[i:j])
                    i = j
                except:
                    new_word.extend(word[i:])
                    break

                if word[i] == first and i < len(word)-1 and word[i+1] == second:
                    new_word.append(first+second)
                    i += 2
                else:
                    new_word.append(word[i])
                    i += 1
            new_word = tuple(new_word)
            word = new_word
            if len(word) == 1:
                break
            else:
                pairs = get_pairs(word)
        word = ' '.join(word)
        if word == '\n  </w>':
            word = '\n</w>'
        self.cache[token] = word
        return word

    def encode(self, texts, verbose=True):
        texts_tokens = []
        if verbose:
            for text in tqdm(texts, ncols=80, leave=False):
                text = self.nlp(text_standardize(ftfy.fix_text(text)))
                text_tokens = []
                for token in text:
                    text_tokens.extend([self.encoder.get(t, 0) for t in self.bpe(token.text.lower()).split(' ')])
                texts_tokens.append(text_tokens)
        else:
            for text in texts:
                text = self.nlp(text_standardize(ftfy.fix_text(text)))
                text_tokens = []
                for token in text:
                    text_tokens.extend([self.encoder.get(t, 0) for t in self.bpe(token.text.lower()).split(' ')])
                texts_tokens.append(text_tokens)
        return texts_tokens
```

一些细节上的区别：

1. 最后一个字符和停止符`</w>`之间不加空格。

### 词表构建

可以看到代码中有两个文件，一个是`encoder_path`，一个是`bpe_path`。
其中`bpe_path`是上述步骤中的每次迭代的best pair，即每次迭代出现频率最高的字节对，其形式如下：

```txt
t h
i n
e d</w>
a n
th e</w>
o u
e r</w>
in g</w>
t o</w>
e r
h e</w>
an d</w>
a r
h i
......
```

而`encoder_path`是最终得到的词表，形式如下：

```json
{".": 1, ",": 2, "t": 3, "h": 4, "e": 5, "\"": 6, "o": 7, "a": 8, "n": 9, "d": 10, "i": 11, "f": 12, "w": 13, "s": 14, "y": 15, "u": 16, "r": 17, "'": 18, "?": 19, "m": 20, ......, "bib</w>": 40474, "benteley</w>": 40475, "bachelorette</w>": 40476, "\n</w>": 40477, "<unk>": 0}
```

### 编码

按照`bpe_path`中的pair顺序，将输入中的字符合并，无法合并后得到最终的输出。具体参考`bpe`和`encode`两个函数。

例如：

```python
# 给定输入
["the"]

# 处理成字符形式
["t", "h", "e</w>"]

# bpe_path表
{("t", "h"): 0, ("i", "n"): 1}

# 合并("t", "h")
["th", "e</w>"]

# 继续合并，直到无法合并，或者长度为1（没有pair）。
......
```

## GPT2中的BBPE(Byte-Level BPE)

从GPT2开始使用了Byte-Level BPE，包括以后得GPT3、ChatGPT等方法中都使用了这种tokenize方法。

Byte-Level BPE的优点：

1. 减小词表大小。
2. 统一所有语言的字符编码，不存在OOV的问题。

Byte-level BPE和BPE的区别就是BPE最小词汇是字符级别，而BBPE是字节级别，其他实现步骤完全一致。

下面展示GPT2的实现：

```python
"""Byte pair encoding utilities"""

import os
import json
import regex as re
from functools import lru_cache

@lru_cache()
def bytes_to_unicode():
    """
    Returns list of utf-8 byte and a corresponding list of unicode strings.
    The reversible bpe codes work on unicode strings.
    This means you need a large # of unicode characters in your vocab if you want to avoid UNKs.
    When you're at something like a 10B token dataset you end up needing around 5K for decent coverage.
    This is a signficant percentage of your normal, say, 32K bpe vocab.
    To avoid that, we want lookup tables between utf-8 bytes and unicode strings.
    And avoids mapping to whitespace/control characters the bpe code barfs on.
    """
    bs = list(range(ord("!"), ord("~")+1))+list(range(ord("¡"), ord("¬")+1))+list(range(ord("®"), ord("ÿ")+1))
    cs = bs[:]
    n = 0
    for b in range(2**8):
        if b not in bs:
            bs.append(b)
            cs.append(2**8+n)
            n += 1
    cs = [chr(n) for n in cs]
    return dict(zip(bs, cs))

def get_pairs(word):
    """Return set of symbol pairs in a word.

    Word is represented as tuple of symbols (symbols being variable-length strings).
    """
    pairs = set()
    prev_char = word[0]
    for char in word[1:]:
        pairs.add((prev_char, char))
        prev_char = char
    return pairs

class Encoder:
    def __init__(self, encoder, bpe_merges, errors='replace'):
        self.encoder = encoder
        self.decoder = {v:k for k,v in self.encoder.items()}
        self.errors = errors # how to handle errors in decoding
        self.byte_encoder = bytes_to_unicode()
        self.byte_decoder = {v:k for k, v in self.byte_encoder.items()}
        self.bpe_ranks = dict(zip(bpe_merges, range(len(bpe_merges))))
        self.cache = {}

        # Should haved added re.IGNORECASE so BPE merges can happen for capitalized versions of contractions
        self.pat = re.compile(r"""'s|'t|'re|'ve|'m|'ll|'d| ?\p{L}+| ?\p{N}+| ?[^\s\p{L}\p{N}]+|\s+(?!\S)|\s+""")

    def bpe(self, token):
        if token in self.cache:
            return self.cache[token]
        word = tuple(token)
        pairs = get_pairs(word)

        if not pairs:
            return token

        while True:
            bigram = min(pairs, key = lambda pair: self.bpe_ranks.get(pair, float('inf')))
            if bigram not in self.bpe_ranks:
                break
            first, second = bigram
            new_word = []
            i = 0
            while i < len(word):
                try:
                    j = word.index(first, i)
                    new_word.extend(word[i:j])
                    i = j
                except:
                    new_word.extend(word[i:])
                    break

                if word[i] == first and i < len(word)-1 and word[i+1] == second:
                    new_word.append(first+second)
                    i += 2
                else:
                    new_word.append(word[i])
                    i += 1
            new_word = tuple(new_word)
            word = new_word
            if len(word) == 1:
                break
            else:
                pairs = get_pairs(word)
        word = ' '.join(word)
        self.cache[token] = word
        return word

    def encode(self, text):
        bpe_tokens = []
        for token in re.findall(self.pat, text):
            token = ''.join(self.byte_encoder[b] for b in token.encode('utf-8'))
            bpe_tokens.extend(self.encoder[bpe_token] for bpe_token in self.bpe(token).split(' '))
        return bpe_tokens

    def decode(self, tokens):
        text = ''.join([self.decoder[token] for token in tokens])
        text = bytearray([self.byte_decoder[c] for c in text]).decode('utf-8', errors=self.errors)
        return text

def get_encoder(model_name, models_dir):
    with open(os.path.join(models_dir, model_name, 'encoder.json'), 'r') as f:
        encoder = json.load(f)
    with open(os.path.join(models_dir, model_name, 'vocab.bpe'), 'r', encoding="utf-8") as f:
        bpe_data = f.read()
    bpe_merges = [tuple(merge_str.split()) for merge_str in bpe_data.split('\n')[1:-1]]
    return Encoder(
        encoder=encoder,
        bpe_merges=bpe_merges,
    )
```
