# dotnet-workshop 開發者貢獻指南

## 分支管理

+ `main`：用於正式發布的主要分支；提取要求須取得 3 個 approving reviews，且只能從 `dev` 分支提出。
+ `dev`：用於整合最新進度的開發分支；提取要求須取得 1 個 approving review，所有貢獻皆以此分支為目標。

## 為 dotnet-w程式碼
orkshop 貢獻
請依照下列流程貢獻程式碼：

1. 將此存放庫建立分支至自己的帳戶。
2. 在自己的存放庫中，以 `main` 為基礎建立新分支。分支命名規則如下：
   + 新增功能：`feat/**`
   + 修正錯誤：`fix/**`
   + 新增或刪減單元測試：`test/**`
   + 新增、刪除或修改文件：`docs/**`
3. 在新分支中進行修改與開發。
4. 向此存放庫的 `dev` 分支提出 Pull Request。

## 文件規範

此存放庫的文件採用 Markdown 語法；詳細用法請參閱 [Markdown 語法文件](https://docs.github.com/zh/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)。

中文文件須嚴格遵循[中文技術文件規範](https://github.com/ruanyf/document-style-guide)與[中文文案排版指北](https://mazhuang.org/wiki/chinese-copywriting-guidelines/)，以維持美觀且一致的排版。例如，請留意[中文與西文之間的空格](https://github.com/ruanyf/document-style-guide/blob/master/docs/text.md#%E5%AD%97%E9%97%B4%E8%B7%9D)以及[全形標點符號的正確用法](https://github.com/ruanyf/document-style-guide/blob/master/docs/marks.md)。此存放庫的中文引號一律使用與漢字等寬的 `「」`，不使用 `“”`。

西文文件須遵循下列規則：

+ 遵循[中文技術文件規範](https://github.com/ruanyf/document-style-guide)與[中文文案排版指北](https://mazhuang.org/wiki/chinese-copywriting-guidelines/)中關於數字及西文的規範。
+ 正確使用半形標點與空格：
  + 半形逗號 `,`、分號 `;`、句號 `.`、驚嘆號 `!`、問號 `?` 等標點若不在行尾，後方須加上空格；緊接全形標點時除外。例如：`"Hello, world"，是一句學習程式設計語言時常用的句子`。
  + 半形括號 `()`、`[]`、`{}`、`<>` 等符號的左括號前與右括號後須加上空格；位於行首、行尾或緊接全形標點時除外。例如：`Tsinghua University (THU) and Peking University (PKU)（我是全形括號）`。
