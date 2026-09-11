# Contributing to dotnet-workshop

## Branch Management

+ `main`: The primary branch used for releases. Pull requests require three approving reviews and may only be opened from the `dev` branch.
+ `dev`: The development branch containing the latest work. Pull requests require one approving review, and contributions should target this branch.

## Contributing Code to dotnet-workshop

Follow this contribution workflow:

1. Fork this repository to your own account.
2. In your fork, create a branch based on `main`. Use the following branch-naming conventions:
   + New features: `feat/**`
   + Bug fixes: `fix/**`
   + Unit-test changes: `test/**`
   + Documentation changes: `docs/**`
3. Make and develop your changes on the new branch.
4. Open a pull request against this repository's `dev` branch.

## Documentation Style

Documentation in this repository uses Markdown. See the [Markdown syntax documentation](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) for details.

Chinese documentation must strictly follow the [Chinese technical documentation style guide](https://github.com/ruanyf/document-style-guide) and the [Chinese Copywriting Guidelines](https://mazhuang.org/wiki/chinese-copywriting-guidelines/) so that it remains visually consistent. Relevant rules include [spacing between Chinese and Western text](https://github.com/ruanyf/document-style-guide/blob/master/docs/text.md#%E5%AD%97%E9%97%B4%E8%B7%9D) and [proper use of full-width punctuation](https://github.com/ruanyf/document-style-guide/blob/master/docs/marks.md). Chinese quotation marks in this repository must use the full-width `「」` characters instead of `“”`.

Documentation written in Western languages must follow these rules:

+ Follow the rules for numerals and Western text in the [Chinese technical documentation style guide](https://github.com/ruanyf/document-style-guide) and the [Chinese Copywriting Guidelines](https://mazhuang.org/wiki/chinese-copywriting-guidelines/).
+ Use half-width punctuation and spacing correctly:
  + Add a space after half-width commas `,`, semicolons `;`, periods `.`, exclamation marks `!`, question marks `?`, and similar punctuation unless the mark is at the end of a line or immediately followed by full-width punctuation. For example: `"Hello, world"，是一句学习编程语言常用的句子`.
  + Add a space before an opening half-width parenthesis or bracket—`()`, `[]`, `{}`, or `<>`—and after its closing counterpart, unless that position is at the beginning or end of a line or immediately adjacent to full-width punctuation. For example: `Tsinghua University (THU) and Peking University (PKU)（我是全角括号）`.
