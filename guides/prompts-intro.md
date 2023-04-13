# プロンプトの導入

プロンプトエンジニアリングは、様々なアプリケーションや研究テーマにおいて、言語モデル（LM）を効率的に使用するためのプロンプトを開発・最適化するための比較的新しい学問分野です。プロンプトエンジニアリングのスキルは、大規模言語モデル（LLM）の能力と限界をよりよく理解するのに役立ちます。研究者は、質問応答や計算的推論などの一般的なタスクから複雑なタスクまで、幅広くLLMの能力を向上させるためにプロンプトエンジニアリングを使用します。開発者は、プロンプトエンジニアリングを用いて、LLMや他のツールとのインタフェースとなる堅牢で効果的なプロンプト技術を設計しています。

このガイドでは、標準的なプロンプトの基本を説明し、プロンプトを使用して大規模言語モデル（LLM）と対話し、指示する方法の概説を提供します。

すべての例は、特に指定がない限り、`text-davinci-003`（OpenAIのプレイグラウンドを使用）でテストされています。デフォルトの設定、例えば`temperature=0.7`、`top-p=1`を使用しています。

# トピック

- [基本的なプロンプト](#basic-prompts)
- [LLMの設定について一言](#a-word-on-llm-settings)
- [標準プロンプト](#standard-prompts)
- [プロンプト要素](#elements-of-a-prompt)
- [プロンプトをデザインするための一般的なヒント](#general-tips-for-designing-prompts)

---



# Prompting Introduction

Prompt engineering is a relatively new discipline for developing and optimizing prompts to efficiently use language models (LMs) for a wide variety of applications and research topics. Prompt engineering skills help to better understand the capabilities and limitations of large language models (LLMs). Researchers use prompt engineering to improve the capacity of LLMs on a wide range of common and complex tasks such as question answering and arithmetic reasoning. Developers use prompt engineering to design robust and effective prompting techniques that interface with LLMs and other tools.

This guide covers the basics of standard prompts to provide a rough idea of how to use prompts to interact and instruct large language models (LLMs). 

All examples are tested with `text-davinci-003` (using OpenAI's playground) unless otherwise specified. It uses the default configurations, e.g., `temperature=0.7` and `top-p=1`.

Topic:
- [Basic Prompts](#basic-prompts)
- [A Word on LLM Settings](#a-word-on-llm-settings)
- [Standard Prompts](#standard-prompts)
- [Prompt Elements](#elements-of-a-prompt)
- [General Tips for Designing Prompts](#general-tips-for-designing-prompts)

---

## 基本プロンプト

プロンプトを使えばすでに多くのことが実現できますが、結果の質は、プロンプトにどれだけの情報を与えるかによって決まります。プロンプトには、モデルに渡す `指示` や `質問` のような情報を含めることができ、`入力` や `例` のような他の詳細も含めることができます。

## Basic Prompts

You can already achieve a lot with prompts, but the quality of results depends on how much information you provide it. A prompt can contain information like the `instruction` or `question` you are passing to the model and include other details such as `inputs` or `examples`. 

ここでは、シンプルなプロンプトの基本的な例を示します：

*プロンプト*
```
The sky is
```

*出力:*
```
blue

The sky is blue on a clear day. On a cloudy day, the sky may be gray or white.
```

ご覧のように、言語モデルは「The sky is」という文脈から意味のある文字列を続けて出力します。この出力は、予想外のものであったり、達成したいタスクからかけ離れたものであったりするかもしれません。


Here is a basic example of a simple prompt:

*Prompt*
```
The sky is
```

*Output:*
```
blue

The sky is blue on a clear day. On a cloudy day, the sky may be gray or white.
```

As you can see, the language model outputs a continuation of strings that make sense given the context `"The sky is"`. The output might be unexpected or far from the task we want to accomplish. 

この基本的な例から、具体的に何を達成したいのか、もっと文脈や指示を与える必要性があることがわかります。

少し改良してみましょう：

*プロンプト*
```
Complete the sentence: 

The sky is
```

*出力：*

```
 so  beautiful today.
```

これで良いのでしょうか？このように、モデルに文章を完成させるように指示したので、結果は私たちが指示したこと（「文章を完成させる」）に忠実に従うことになり、より良く見えます。このように、モデルにタスクを指示するための最適なプロンプトを設計するアプローチを、プロンプトエンジニアリングと呼びます。

This basic example also highlights the necessity to provide more context or instructions on what specifically we want to achieve.

Let's try to improve it a bit:

*Prompt:*
```
Complete the sentence: 

The sky is
```

*Output:*

```
 so  beautiful today.
```

Is that better? Well, we told the model to complete the sentence so the result looks a lot better as it follows exactly what we told it to do ("complete the sentence"). This approach of designing optimal prompts to instruct the model to perform a task is what's referred to as **prompt engineering**. 

The example above is a basic illustration of what's possible with LLMs today. Today's LLMs can perform all kinds of advanced tasks that range from text summarization to mathematical reasoning to code generation.





上の例は、現在のLLMで可能なことの基本的な説明である。現在のLLMは、テキストの要約から数学的推論、コード生成まで、あらゆる種類の高度なタスクを実行することができます。

LLMの設定について
プロンプトを扱う場合、APIを介して、または直接LLMと対話することになります。いくつかのパラメータを設定することで、プロンプトの結果を変化させることができます。

温度 - つまり、温度が低ければ低いほど、次のトークンの確率が最も高いものが常に選ばれるという意味で、結果がより決定的になります。温度を上げると、ランダム性が高まり、より多様で創造的なアウトプットが可能になります。つまり、他の可能性のあるトークンの重みを増やすのです。応用例としては、事実に基づくQAなどでは、温度を低くして、より事実に基づいた簡潔な回答を促すとよいでしょう。詩の生成やその他の創造的な作業には、温度を高くするのが効果的かもしれません。

Top_p - 同様に、核サンプリングと呼ばれる温度によるサンプリング手法であるtop_pでは、モデルが応答を生成する際の決定性を制御することができます。正確で事実に基づいた答えを求めるのであれば、この温度は低く抑えてください。より多様な回答を求めるのであれば、より高い値を設定してください。

一般的には、両方を変更するのではなく、どちらかを変更することをお勧めします。

基本的な例から始める前に、使用するLLMのバージョンによって結果が異なる可能性があることを念頭に置いておいてください。

標準的なプロンプト
以上、非常にシンプルなプロンプトを試してみました。標準的なプロンプトは、次のような形式です：

<Question>?
これを、多くのQAデータセットで標準的に使われているQAフォーマットにすると、次のようになります：

Q: <Question>?
A: 
上記の標準的な形式を考えると、プロンプトのための1つの一般的で効果的なテクニックは、私たちが模範を提供する数ショットプロンプトと呼ばれるものです。スモールショットプロンプトは、以下のような形式をとることができます：

<Question>?
<答え

<Question>?
<Answer>

<Question>?
<Answer>

<Question>とは？

そして、そのQA形式バージョンは次のようになることは、もうお分かりでしょう：

Q：<Question>?
A: <答え> です。

Q：＜質問＞は？
A：＜答え＞です。

Q：＜質問＞は？
A：＜答え＞です。

Q：＜質問＞は？
A:
QA形式を使うことが必須ではないことに留意してください。フォーマットは、その時のタスクによって異なります。例えば、簡単な分類作業を行い、その作業を示す模範解答を次のように与えることができます：

プロンプト

これはすごいですね！// ポジティブ
これは悪いことだ！// ネガティブ
あの映画は最高だった！// ポジティブ
なんて恐ろしい番組なんだ //
出力する：

否定的な意見
数発のプロンプトは、言語モデルがわずかな例からタスクを学習する能力であるインコンテキスト学習を可能にします。今後のガイドで、この機能をより詳しく見ていきましょう。

プロンプトの構成要素
プロンプトエンジニアリングで可能な事例や応用例を紹介していくうちに、プロンプトを構成する要素が決まっていることに気づかされます。

プロンプトには、以下のいずれかの要素が含まれます：

命令-モデルに実行させたい特定のタスクや命令

コンテキスト - モデルをより良い反応に導くことができる外部情報や追加のコンテキストを含むことができる。

入力データ - 応答を見つけることに興味がある入力または質問です。

出力インジケータ - 出力のタイプまたは形式を示す。

すべての構成要素がプロンプトに必要なわけではなく、形式は目の前のタスクに依存します。具体的な例については、次回のガイドで紹介します。

プロンプトをデザインするための一般的なヒント
プロンプトをデザインする際に気をつけたいポイントをいくつかご紹介します：

シンプルに始める
プロンプトの設計を始めるにあたり、最適な結果を得るためには多くの実験が必要な反復プロセスであることを念頭に置いておく必要があります。OpenAIやCohereのようなシンプルなプレイグラウンドを使用することは、良い出発点です。

シンプルなプロンプトから始めて、より良い結果を目指しながら、要素や文脈をどんどん追加していけばいいのです。そのためには、プロンプトをバージョンアップしていくことが重要です。このガイドを読むと、具体性、シンプルさ、簡潔さが、より良い結果をもたらすことが多いことがわかります。

大きなタスクにさまざまなサブタスクが含まれる場合、タスクをよりシンプルなサブタスクに分解し、より良い結果が得られるように積み重ねることができます。こうすることで、プロンプトのデザインプロセスを最初に複雑にしすぎることを避けることができます。

インストラクション
書く」「分類する」「要約する」「翻訳する」「順番に並べる」など、実現したいことをコマンドでモデルに指示することで、さまざまな単純作業に効果的なプロンプトを設計することができます。

また、何が一番効果的かを知るために、たくさん実験する必要があることも覚えておいてください。キーワード、文脈、データを変えてさまざまな指示を試し、特定のユースケースやタスクに最適なものが何かを確認します。通常、コンテキストは、実行しようとしているタスクに対してより具体的で関連性の高いものであればあるほど、より良いものとなります。サンプリングの重要性や、より多くのコンテキストを追加することについては、今後のガイドで触れていきます。

また、指示はプロンプトの冒頭に置くことを推奨する人もいます。また、指示と文脈を分けるために、「##」のような明確な区切り文字を使用することも推奨されています。

例えば、以下のような感じです：

プロンプト：

### プロンプト：##命令##
以下の文章をスペイン語に翻訳してください：

テキスト "こんにちは！"
出力します：

Hola！
特異性
モデルに実行させたい指示やタスクについて、非常に具体的に説明すること。プロンプトがより説明的で詳細であればあるほど、より良い結果を得ることができます。これは、求める結果や生成のスタイルが決まっている場合に特に重要です。特定のトークンやキーワードが良い結果をもたらすというわけではありません。それよりも、良いフォーマットと説明的なプロンプトを用意することが重要なのです。プロンプトの中で例を示すことは、特定のフォーマットで望ましい出力を得るために非常に効果的です。

プロンプトをデザインする際には、プロンプトの長さにも留意する必要があります（プロンプトの長さには制限があります）。また、どの程度具体的かつ詳細に書くべきかを考える必要があります。不必要な詳細が多すぎるのは、必ずしも良いアプローチではありません。詳細は、関連性があり、目の前のタスクに貢献するものでなければなりません。これは、何度も試してみる必要があります。プロンプトをアプリケーションに最適化するために、多くの実験と反復を行うことをお勧めします。

例として、テキストから特定の情報を抽出するための簡単なプロンプトを試してみましょう。

プロンプト

以下のテキストから地名を抽出してください。

希望する形式
場所 <コンマで区切られた会社名リスト>のようなもの。

入力する： "これらの開発は研究者にとって心強いものですが、多くはまだ謎に包まれています。リスボンのシャンパリモー未知センターで神経免疫学者を務めるヘンリケ・ヴェイガ＝フェルナンデスは、「脳と末梢で見られる効果の間には、しばしばブラックボックスが存在する」と言う。"治療の場面で使いたいのであれば、そのメカニズムを理解する必要があります。"
アウトプットする

場所 リスボン、シャンパリマード未知なるセンター
入力テキストは、このNatureの記事から取得したものです。

不正確な表現にならないようにする
上記のように、詳細な説明や形式を改善するためのヒントがあるため、プロンプトに対してあまりに巧妙になりすぎて、不正確な記述をする可能性があるという罠に陥りがちです。具体的で直接的な表現にしたほうがいい場合もあります。この例えは、効果的なコミュニケーションに非常に似ています。

例えば、プロンプトエンジニアリングという概念に興味を持ったとします。次のようなことを試してみてはいかがでしょうか：

プロンプトエンジニアリングの概念を説明する。説明文は短く、数センテンスにとどめ、あまり説明的にならないようにします。
上記のプロンプトからは、どのようなスタイルで何文使えばいいのかがわかりません。上記のプロンプトでもある程度は良い回答が得られるかもしれませんが、より良いプロンプトは、非常に具体的で簡潔、かつ要点を押さえたものでしょう。例えば、次のようなものです：

高校生にプロンプトエンジニアリングのコンセプトを説明するために、2～3文を使用してください。
やるか、やらないか？
プロンプトをデザインする際のもう一つのコツは、「してはいけないこと」を書かず、「すべきこと」を書くことです。こうすることで、より具体的に、モデルから良い反応が得られるような詳細に焦点を当てることができます。

以下は、映画の推薦チャットボットが、「やってはいけないこと」に焦点を当てた命令の書き方のせいで、まさにやってほしくないことに失敗してしまった例です。

プロンプト

以下は、顧客に映画を勧めるエージェントです。趣味を聞いてはいけません。個人情報を求めないでください。

お客さん： 私の興味に基づいた映画を薦めてください。
エージェントです： 
出力します：

もちろん、あなたの興味に基づいた映画をお勧めしますよ。どのような映画をご覧になりたいですか？アクション、コメディ、ロマンス、それとも他のものが好きですか？
ここで、より良いプロンプトを紹介します：

プロンプトです：

以下は、顧客に映画を勧めるエージェントである。このエージェントは、世界のトップトレンドの映画から映画を推薦する責任があります。ユーザーに好みを聞くのは控え、個人情報を聞くのも避けるべきです。もし、おすすめする映画がない場合は、"Sorry, couldn't find a movie to recommend today. "と応答する必要があります。

お客様です： 私の興味に基づいた映画を薦めてください。
エージェントは
出力してください：

申し訳ありませんが、あなたの興味に関する情報はありません。しかし、今、世界のトップトレンドの映画のリストがあります： [映画のリスト]です。何かお好きなものが見つかるといいですね！
上記の例の一部は、「OpenAI APIを使ったプロンプトエンジニアリングのベストプラクティス」記事から採用しました。





## Basic Prompts

You can already achieve a lot with prompts, but the quality of results depends on how much information you provide it. A prompt can contain information like the `instruction` or `question` you are passing to the model and include other details such as `inputs` or `examples`. 

Here is a basic example of a simple prompt:

*Prompt*
```
The sky is
```

*Output:*
```
blue

The sky is blue on a clear day. On a cloudy day, the sky may be gray or white.
```

As you can see, the language model outputs a continuation of strings that make sense given the context `"The sky is"`. The output might be unexpected or far from the task we want to accomplish. 

This basic example also highlights the necessity to provide more context or instructions on what specifically we want to achieve.

Let's try to improve it a bit:

*Prompt:*
```
Complete the sentence: 

The sky is
```

*Output:*

```
 so  beautiful today.
```

Is that better? Well, we told the model to complete the sentence so the result looks a lot better as it follows exactly what we told it to do ("complete the sentence"). This approach of designing optimal prompts to instruct the model to perform a task is what's referred to as **prompt engineering**. 

The example above is a basic illustration of what's possible with LLMs today. Today's LLMs can perform all kinds of advanced tasks that range from text summarization to mathematical reasoning to code generation.

---
## A Word on LLM Settings

When working with prompts, you will be interacting with the LLM via an API or directly. You can configure a few parameters to get different results for your prompts. 

**Temperature** - In short, the lower the temperature the more deterministic the results in the sense that the highest probable next token is always picked. Increasing the temperature could lead to more randomness encouraging more diverse or creative outputs. We are essentially increasing the weights of the other possible tokens. In terms of application, we might want to use a lower temperature for something like fact-based QA to encourage more factual and concise responses. For poem generation or other creative tasks, it might be beneficial to increase the temperature. 

**Top_p** - Similarly, with top_p, a sampling technique with temperature called nucleus sampling, you can control how deterministic the model is at generating a response. If you are looking for exact and factual answers keep this low. If you are looking for more diverse responses, increase to a higher value. 

The general recommendation is to alter one, not both.

Before starting with some basic examples, keep in mind that your results may vary depending on the version of LLM you are using. 

---
## Standard Prompts

We have tried a very simple prompt above. A standard prompt has the following format:

```
<Question>?
```
 
This can be formatted into a QA format, which is standard in a lot of QA dataset, as follows:

```
Q: <Question>?
A: 
```

Given the standard format above, one popular and effective technique for prompting is referred to as few-shot prompting where we provide exemplars. Few-shot prompts can be formatted as follows:

```
<Question>?
<Answer>

<Question>?
<Answer>

<Question>?
<Answer>

<Question>?

```


And you can already guess that its QA format version would look like this:

```
Q: <Question>?
A: <Answer>

Q: <Question>?
A: <Answer>

Q: <Question>?
A: <Answer>

Q: <Question>?
A:
```

Keep in mind that it's not required to use QA format. The format depends on the task at hand. For instance, you can perform a simple classification task and give exemplars that demonstrate the task as follows:

*Prompt:*
```
This is awesome! // Positive
This is bad! // Negative
Wow that movie was rad! // Positive
What a horrible show! //
```

*Output:*
```
Negative
```

Few-shot prompts enable in-context learning which is the ability of language models to learn tasks given only a few examples. We will see more of this in action in the upcoming guides.

---
## Elements of a Prompt

As we cover more and more examples and applications that are possible with prompt engineering, you will notice that there are certain elements that make up a prompt. 

A prompt can contain any of the following components:

**Instruction** - a specific task or instruction you want the model to perform

**Context** - can involve external information or additional context that can steer the model to better responses

**Input Data** - is the input or question that we are interested to find a response for

**Output Indicator** - indicates the type or format of the output.

Not all the components are required for a prompt and the format depends on the task at hand. We will touch on more concrete examples in upcoming guides.

---
## General Tips for Designing Prompts

Here are some tips to keep in mind while you are designing your prompts:


### Start Simple
As you get started with designing prompts, you should keep in mind that it is an iterative process that requires a lot of experimentation to get optimal results. Using a simple playground like OpenAI's or Cohere's is a good starting point. 

You can start with simple prompts and keep adding more elements and context as you aim for better results. Versioning your prompt along the way is vital for this reason. As we read the guide you will see many examples where specificity, simplicity, and conciseness will often give you better results.

When you have a big task that involves many different subtasks, you can try to break down the task into simpler subtasks and keep building up as you get better results. This avoids adding too much complexity to the prompt design process at the beginning.

### The Instruction
You can design effective prompts for various simple tasks by using commands to instruct the model what you want to achieve such as "Write", "Classify", "Summarize", "Translate", "Order", etc.

Keep in mind that you also need to experiment a lot to see what works best. Try different instructions with different keywords, contexts, and data and see what works best for your particular use case and task. Usually, the more specific and relevant the context is to the task you are trying to perform, the better. We will touch on the importance of sampling and adding more context in the upcoming guides.

Others recommend that instructions are placed at the beginning of the prompt. It's also recommended that some clear separator like "###" is used to separate the instruction and context. 

For instance:

*Prompt:*
```
### Instruction ###
Translate the text below to Spanish:

Text: "hello!"
```

*Output:*
```
¡Hola!
```

### Specificity
Be very specific about the instruction and task you want the model to perform. The more descriptive and detailed the prompt is, the better the results. This is particularly important when you have a desired outcome or style of generation you are seeking. There aren't specific tokens or keywords that lead to better results. It's more important to have a good format and descriptive prompt. Providing examples in the prompt is very effective to get desired output in specific formats. 

When designing prompts you should also keep in mind the length of the prompt as there are limitations regarding how long this can be. Thinking about how specific and detailed you should be is something to consider. Too many unnecessary details are not necessarily a good approach. The details should be relevant and contribute to the task at hand. This is something you will need to experiment with a lot. We encourage a lot of experimentation and iteration to optimize prompts for your applications.

As an example, let's try a simple prompt to extract specific information from a piece of text.

*Prompt:*
```
Extract the name of places in the following text. 

Desired format:
Place: <comma_separated_list_of_company_names>

Input: "Although these developments are encouraging to researchers, much is still a mystery. “We often have a black box between the brain and the effect we see in the periphery,” says Henrique Veiga-Fernandes, a neuroimmunologist at the Champalimaud Centre for the Unknown in Lisbon. “If we want to use it in the therapeutic context, we need to understand the mechanism."
```

*Output:*
```
Place: Champalimaud Centre for the Unknown, Lisbon
```

The input text is obtained from [this Nature article](https://www.nature.com/articles/d41586-023-00509-z).

### Avoid Impreciseness

Given the tips above about being detailed and improving format, it's easy to fall into the trap of wanting to be too clever about prompts and potentially creating imprecise descriptions. It's often better to be specific and direct. The analogy here is very similar to effective communication -- the more direct, the more effective the message gets across.

For example, you might be interested in learning the concept of prompt engineering. You might try something like:

```
Explain the concept of prompt engineering. Keep the explanation short, only a few sentences, and don't be too descriptive.
```

It's not clear from the prompt above how many sentences to use and what style. You might still somewhat get good responses with the above prompts but the better prompt would be one that is very specific, concise, and to the point. Something like:

```
Use 2-3 sentences to explain the concept of prompt engineering to a high school student.
```

### To do or not to do?
Another common tip when designing prompts is to avoid saying what not to do but say what to do instead. This encourages more specificity and focuses on the details that lead to good responses from the model.

Here is an example of a movie recommendation chatbot failing at exactly what I don't want it to do because of how I wrote the instruction -- focusing on what not to do. 

*Prompt:*
```
The following is an agent that recommends movies to a customer. DO NOT ASK FOR INTERESTS. DO NOT ASK FOR PERSONAL INFORMATION.

Customer: Please recommend a movie based on my interests.
Agent: 
```

*Output:*
```
Sure, I can recommend a movie based on your interests. What kind of movie would you like to watch? Do you prefer action, comedy, romance, or something else?
```

Here is a better prompt:

*Prompt:*
```
The following is an agent that recommends movies to a customer. The agent is responsible to recommend a movie from the top global trending movies. It should refrain from asking users for their preferences and avoid asking for personal information. If the agent doesn't have a movie to recommend, it should respond "Sorry, couldn't find a movie to recommend today.".

Customer: Please recommend a movie based on my interests.
Agent:
```

*Output:*
```
Sorry, I don't have any information about your interests. However, here's a list of the top global trending movies right now: [list of movies]. I hope you find something you like!
```

Some of the examples above were adopted from the ["Best practices for prompt engineering with OpenAI API" article.](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)


---
[Next Section (Basic Prompting)](./prompts-basic-usage.md)
