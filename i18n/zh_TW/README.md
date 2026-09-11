# 科協暑培（.NET 部分）學習型專案

## 簡介

本專案是為[清華大學電子工程系學生科學技術協會](https://eesast.com)暑期培訓（以下簡稱「暑培」）的 .NET 部分（亦稱隊式部分）所編寫的學習與訓練型專案。學生將透過製作一套簡單的軟體，學習 .NET 技術堆疊，以及未來一年參加[隊式程式設計大賽](https://eesast.com/#/contest/list)時會使用的其他技術。

暑培時間短、涵蓋範圍廣，且每位講者各自負責一部分內容。講者自然都希望傾囊相授，讓學生快速上手，因此過去常採用知識密集的教學方式，多少形成了「填鴨式」甚至「量子波動速讀」般的效果。一門技術（尤其是程式設計）的知識點既多又分散；即使講者努力掌握主幹，課程仍容易變成冗長的知識堆砌，不但令人厭倦，也不利於學生理解如何在實際開發中運用技術。

有鑑於此，[FranGuam](https://github.com/FranGuam) 為暑培網站部分創作了 [web-workshop](https://github.com/eesast/web-workshop)，大幅提升學生的學習意願。隊式部分參考了這項成功經驗，開發出這套學習型專案。

**專案首頁：** [https://eesast.github.io/dotnet-workshop/](https://eesast.github.io/dotnet-workshop/)  

**參考解答：** [https://github.com/eesast/dotnet-workshop-solution](https://github.com/eesast/dotnet-workshop-solution)（並非 `public`，僅供講師內部參考）  
**成果示範：** [https://eesast.github.io/dotnet-workshop/demo/](https://eesast.github.io/dotnet-workshop/demo/)

## 學習內容

本專案將實作一套簡易的雲端服務記錄分析系統。你將學到下列內容：

1. **C\# 與物件導向程式設計。** .NET 是由[微軟](https://www.microsoft.com/)開發的開放原始碼軟體開發架構與執行階段，而 C\# 是在 .NET 上開發軟體的首選語言。C\# 與 .NET SDK 也為物件導向程式設計提供了良好支援。本專案會介紹基本的 .NET 開發環境與 C\# 語法，並帶你認識一些物件導向程式設計概念。
2. **多執行緒並行程式設計。** 處理序是作業系統進行分時多工的抽象，也是分配資源的基本單位；執行緒則是處理序內部進行分時多工的抽象。程式經常需要並行執行多項任務，而不是依序逐一執行，此時便需要多個執行緒互相協作。雖然現代應用程式已很少直接操作執行緒，通常會由執行緒集區、非同步或平行運算架構加以封裝，但理解執行緒原理仍然很有價值。本專案將介紹多執行緒，以及多執行緒程式設計中的同步與互斥問題。
3. **非同步與通訊。** 在網際網路時代，網路應用程式與應用程式之間的網路通訊隨處可見。非同步是一種程式設計模型，用來在網路通訊期間重複使用執行緒，並解決回呼地獄等問題。本專案將介紹由 Google 開發的**遠端程序呼叫（RPC）**架構 [gRPC](https://grpc.io/)，並使用其非同步介面開發網路應用程式。
4. **Avalonia UI。** [Avalonia UI](https://avaloniaui.net/) 是以 .NET 為基礎的開放原始碼跨平台圖形使用者介面（GUI）架構，可視為微軟官方 GUI 架構 [WPF](https://learn.microsoft.com/zh-tw/dotnet/desktop/wpf/overview/) 的跨平台實作。本專案將帶你使用 Avalonia UI 設計一套簡單的圖形介面。
5. **Unity。** Unity 是廣受使用的遊戲引擎，主要使用 C\# 開發，也是暑培隊式部分的內容。不過，本專案尚未納入 Unity，因為整合工作並不容易，尤其是 Unity 專案難以使用 GitHub 管理。詳情請參閱[開發札記](./DEVLOG.md)中的 Unity 相關說明。能否在未來加入 Unity，就留待後來的貢獻者發揮智慧。

## 開始學習

本專案的所有文件都位於 `docs/` 目錄。請先閱讀 `docs/00-prepare` 中的文件，完成環境準備、了解背景與作業繳交方式。接著依編號順序閱讀各章的指引（`guidance.md`）並完成任務（`tasks.md`）。

+ `00-prepare`
  + [指引](./docs/00-prepare/guidance.md)
  + [任務](./docs/00-prepare/tasks.md)
+ `01-basic`
  + [指引](./docs/01-basic/guidance.md)
  + [任務](./docs/01-basic/tasks.md)
+ `02-multithreading`
  + [指引](./docs/02-multithreading/guidance.md)
  + [任務](./docs/02-multithreading/tasks.md)
+ `03-async-grpc`
  + [指引](./docs/03-async-grpc/guidance.md)
  + [任務](./docs/03-async-grpc/tasks.md)
+ `04-avalonia`
  + [指引](./docs/04-avalonia/guidance.md)
  + [任務](./docs/04-avalonia/tasks.md)
+ `05-advanced`
  + [指引](./docs/05-advanced/guidance.md)
  + [任務](./docs/05-advanced/tasks.md)
+ `appendix`
  + [附錄 A：部分名詞的多語言對照表](./docs/appendix/appendix-a-glossary.md)

### 關於 Vibe Coding

隨著 Coding Agent 快速發展，截至 2026 年 7 月，先進的大型語言模型已能輕鬆完成本專案的大部分內容。要求學生完全手動完成作業，不但耗費太多時間，也難以監督。暑培允許使用 AI 協助完成作業，但須遵守下列限制：

- 先在 AI 協助下理解專案的整體架構，再挑選你認為重要的程式碼仔細閱讀。
- 避免只用簡短的 prompt 向 AI 許願。你應撰寫足夠詳細的 prompt，明確說明想要的功能與實作方式；也可以透過多輪對話釐清需求、完善 prompt，確保自己充分理解專案細節。
- 所有 AI 產生的程式碼都必須經過人工 review；這對理解所學內容至關重要。

我們相信，學生參加暑培是為了精進開發能力，而不是只求完成作業。完成暑培後，你應能培養一名 **Developer** 該有的**品味（taste）**，引導自己在廣闊的軟體開發世界中持續前進。

> 在 AI 時代，多數簡單需求都能透過 AI 在短時間內完成。然而，真實的軟體系統往往有複雜的商業邏輯、多變的需求，以及團隊協作和長期維護等挑戰。
> 常見的例子是：AI 很快就產生了某個功能模組，卻加入許多不必要的條件檢查與例外狀況處理，使程式碼冗長又難以理解，也就是過度的防禦性程式設計。若不加以審查與改善，久而久之，整個系統就會變成難以維護的「屎山」。
> 面對複雜的商業需求，如何簡潔、高效地實作功能，並在長期維護中維持程式碼的可讀性與可擴充性，都需要開發者具備良好的程式碼品味。

## 貢獻

如果你要開發本專案的程式碼架構，請先仔細閱讀[開發者貢獻指南](./CONTRIBUTING.md)。

## 意見回饋

若你對軟體本身或學習曲線的設計有任何疑問，歡迎依照 [Issue #69](https://github.com/eesast/dotnet-workshop/issues/69) 中的說明提供意見。

## 後記

本專案受到 [FranGuam](https://github.com/FranGuam) 所寫的 [web-workshop](https://github.com/eesast/web-workshop) 啟發，任務設計也部分參考了 [MIT 6.5840: Spring 2023](http://nil.csail.mit.edu/6.5840/2023/index.html)，在此致謝。

若想了解開發過程中的經歷、心得與幕後花絮，歡迎閱讀[開發札記](./DEVLOG.md)。

## 開放原始碼授權

本專案的所有程式碼皆依 [MIT License](../../LICENSE-code.txt)（`SPDX-License-Identifier: MIT`）發布；所有文件皆依 [Creative Commons 姓名標示—相同方式分享 4.0 國際授權條款](../../LICENSE-document.txt)（`SPDX-License-Identifier: CC-BY-SA-4.0`）發布。
