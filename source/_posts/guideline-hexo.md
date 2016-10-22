---
title: Hướng dẫn viết blog
date: 2016-10-21 00:20:04
tags: guideline
category: guideline

---

## Cài đặt môi trường

Blog sử dụng hexo dựa trên nodejs nên cần cài đặt nodejs
1. cài đặt [nodejs](https://nodejs.org/en/)

2. cài đặt [hexo](https://hexo.io)

```bash
npm install hexo-cli -g
```
<!-- more -->
## Get source code từ github

3. clone blog source về máy từ github và cài đặt dependencies

```bash
git clone https://github.com/hieunh161/at-blog.git
cd {thư mục source}
npm install
```

## Tạo bài viết mới và public lên heroku

4. Tạo bài viết mới bằng lệnh

```bash
cd {thư mục source}
hexo new post {tên bài viết}
```

5. Vào thư mục source/_posts viết bài bằng markdown. 
Có thể tham khảo markdown cheatsheet [ở đây](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

6. Commit source lên git sau đó deploy source lên server

```bash
npm run deploy
```