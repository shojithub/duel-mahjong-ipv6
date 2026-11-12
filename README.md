# 双龍麻雀 P2P — Duel Riichi Mahjong (IPv6 Priority WebRTC)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![WebRTC](https://img.shields.io/badge/WebRTC-IPv6%20%2F%20IPv4%20Dual--Stack-0284c7)](https://shojithub.github.io/duel-mahjong-ipv6/)

ブラウザを開くだけですぐに遊べる、**WebRTC P2P直結（IPv6ネイティブ優先・IPv4自動フォールバック）対応の2人対戦リーチ麻雀ゲーム**です。
点数の勝敗だけでなく、**「打牌精度（実力スコア）」** と **「ツモ・配牌・裏ドラの偏り（運量指数）」** をAIがリアルタイム解析し、遊びながら牌効率や押し引きも鍛えられます。

## 🎮 今すぐブラウザで遊ぶ（インストール・登録不要）
👉 **[https://shojithub.github.io/duel-mahjong-ipv6/](https://shojithub.github.io/duel-mahjong-ipv6/)**

- PC・スマートフォン・タブレット対応（単一HTML完結）
- ローカルに `index.html` をダウンロードして直接開いても同様に動作します

---

## ✨ 主な特徴

### 1. サーバーレス 1クリック対戦マッチング
- **⚡ クイックマッチ（自動対戦）**: ボタンを押すだけで待機中のプレイヤーと自動マッチングし、裏側でVanilla ICE SDPを自動交換して即対局が始まります。
- **🔑 4桁ルーム番号（フレンド対戦）**: 「部屋を作る」で発行される4桁の番号（または `#room=XXXX` 付き招待URL）を共有するだけで、別PC・スマホ・別ブラウザ間でP2P直結します。
- **🤖 CPU練習対戦**: 1人でもAI雀士を相手にいつでも試遊・練習できます。

### 2. 「実力」と「運」を切り分けるAIトレーニング解析
- **🏅 打牌クオリティ採点（`0〜100点` / `S+〜C` ランク）**: 毎巡14枚の全打牌候補に対してシャンテン数（通常手・七対子・国士無双）・見え牌を考慮した有効牌枚数・打点価値を算出し、得点勝者とは別に「打牌精度 勝者」を表彰します。
- **🛡️ 押し引き・守備スキル評価**: 相手のリーチや副露が入ると自動で攻守バランス／オリ優先評価へ切り替わり、現物（危険度`0%`）・スジ・字牌での「ナイスオリ！」や無謀な危険牌押しを判定します。
- **🔥 運量指数（`Luck Index`）**: 配牌運・毎巡の有効牌ツモ確率偏差・一発／裏ドラ／相手ツモ被弾を統計的に数値化し、「運で負けたのか、実力で勝ったのか」を可視化します。
- **🩺 AI 1手振り返りクリニック & 🎓 練習ヒント切替**: 局終了時に最大の分岐点となった巡目の手牌を再現して最善手と比較解説するほか、練習モードでは手牌ホバー時にリアルタイム受入枚数・危険度を表示できます。

### 3. 本格的な108枚デュエル・リーチ麻雀ルール
- 萬子の2〜8を抜いた **計108枚**（萬子 `1m・9m`、筒子 `1p〜9p`、索子 `1s〜9s`、字牌 `東南西北白發中` × 各4枚、赤5p・赤5s入り）によるスピーディで高打点な2人麻雀。
- 初期持ち点 35,000点 / 東1局〜南2局の4局戦（親連荘・本場・供託あり） / 主要リーチ麻雀役・役満・フリテン判定・待ち牌ナビ完備。

---

## 🌐 通信アーキテクチャ仕様

1. **デュアルスタック Vanilla ICE**
   - STUNサーバー: `stun:stun.l.google.com:19302`
   - `onicegatheringstatechange === 'complete'` まで待機してSDPを一括出力（Vanilla ICE）
   - 収集された ICE Candidate 内の **IPv6 (`host` / `srflx`)** と **IPv4**、および確立後のアクティブ経路・RTT(ms)をコンソールと画面内診断パネルに可視化（完全オフライン検証用の手動SDPコピペ欄も「🛠 詳細設定」内に搭載）
2. **Negotiated モードによる `RTCDataChannel` 2系統分離**
   - **Channel 0 (`Unreliable` / UDP相当)**: `{ ordered: false, maxRetransmits: 0, negotiated: true, id: 0 }`
     - 20バイト固定長 `ArrayBuffer` (`DataView`) バイナリパケットで相手カーソル座標・手牌ホバー位置・RTT計測を30Hz同期
     - `uint32` シーケンス番号を含め、`seq <= lastRemoteSeq` の遅延到着パケットを即時破棄して Head-of-Line Blocking を防止
   - **Channel 1 (`Reliable` / TCP相当)**: `{ ordered: true, negotiated: true, id: 1 }`
     - 配牌・打牌・鳴き・リーチ・和了判定・実力/運解析サマリ・チャットを完全順序保証で同期

## 📄 ライセンス
[MIT License](./LICENSE)
