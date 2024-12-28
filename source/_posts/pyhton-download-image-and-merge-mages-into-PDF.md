---
title: Python 批量下载图片并合并为 PDF 文件
name: Pyhton download image and merge mages into PDF
date: 2020-11-06 19:10:00

image: images/article/python-lang-logo.jpg
categories:
  - Python
tags:
  - Python
---

Python 代码片段：下载图片文件并合并为 PDF 文件。

## 代码片段

批量下载图片到指定目录

```python
import os
import requests

start_page = 1
end_page = 100
source_id = '1384001302181'
dir_name = 'download save path'
url_prefix = f'https://xxx.xxx.xxx.cn/{source_id}/files/mobile/'
image_links = [url_prefix + str(i) + '.jpg' for i in range(start_page, end_page + 1)]
save_dir = os.path.join(os.getcwd(), dir_name)
# print(image_links)

# test 
img_urls = [
    'https://images.unsplash.com/photo-1719937050814-72892488f741',
    'https://images.unsplash.com/photo-1720048171527-208cb3e93192',
    'https://images.unsplash.com/photo-1734409019142-1927557a77b9',
    'https://images.unsplash.com/photo-1734108039189-f6c123288381',
    'https://images.unsplash.com/photo-1734268486202-7e85b12a7fa2',
]

def download_image(url, save_path):
    content_type_map = {
        'image/jpeg': 'jpg',
        'image/png': 'png',
        'image/gif': 'gif',
        'image/webp': 'webp',
        'image/svg+xml': 'svg',
    }
    try:
        response = requests.get(url, stream=True)
        response.raise_for_status()  # 抛出异常处理 HTTP 错误
        print(response.headers)
        save_path = save_path.endswith('.') and save_path or save_path + '.' + content_type_map[response.headers['Content-Type']];
        with open(save_path, 'wb') as f:
            for chunk in response.iter_content(1024):
                f.write(chunk)
        print(f"Image downloaded: {save_path}")
    except requests.exceptions.RequestException as e:
        print(f"Error downloading image {url}: {e}")

if (not os.path.exists(save_dir)):
    os.makedirs(save_dir);

for index, url in enumerate(image_links):
    file_name = url[url.rfind('/') + 1:]
    save_path = os.path.join(os.getcwd(), dir_name, file_name)
    print(f'download: {url} to {dir_name}/{file_name}')
    download_image(url, save_path)
```

将某个目录下的图片合并为 PDF 文件

```python
import os
import re

from fpdf import FPDF
from PIL import Image

book_name = "PDF BOOK NAME";
book_images_dir = "images_dir";

pdf = FPDF(orientation='P', unit='mm', format='A4')
pdf.set_auto_page_break(0)      # 自动分页设为 False
image_path = os.path.join(os.getcwd(), book_images_dir, book_name) 
image_list = [f for f in os.listdir(image_path) if f.lower().endswith(('.png', '.jpg', '.jpeg'))]
image_list.sort(key=lambda l: int(re.findall(r'\d+', l)[0]))

print(pdf.w, pdf.h)
print(image_path)

for image_name in image_list:
    pdf.add_page()
    img_path = os.path.join(image_path, image_name)
    image = Image.open(img_path)
    ratio = pdf.w / image.width
    image.thumbnail((pdf.w, image.height * ratio))
    img_width, img_height = image.size
    # pdf.image(img_path, x=(pdf.w - img_width) / 2, y=(pdf.h - img_height) / 2, w=img_width, h=img_height)
    pdf.image(img_path, x=(pdf.w - img_width) / 2, y=(pdf.h - img_height) / 2, w=img_width, h=img_height)

pdf.output(os.path.join(os.getcwd(), r"{0}.pdf".format(book_name)), "F")
```

