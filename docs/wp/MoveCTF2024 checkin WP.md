---
date: 2024-01-22
authors:
  - P1sc3s007
---
查看智能合约文件<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705040112972-bceb5477-6aac-4d5e-9ede-b344d4c7ccaa.png#averageHue=%23f6f7f8&clientId=u7cc37a92-8627-4&from=paste&height=152&id=u29fe7f88&originHeight=228&originWidth=420&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=12859&status=done&style=none&taskId=uf10baf6d-6533-4627-b017-a520bec205a&title=&width=280)<br />![image.png](https://cdn.nlark.com/yuque/0/2024/png/40787854/1705040137534-1cbf6e9f-ab2a-4b23-bc7f-b2fb8c88c7d4.png#averageHue=%23fefefe&clientId=u7cc37a92-8627-4&from=paste&height=385&id=u07286e4d&originHeight=578&originWidth=829&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=37731&status=done&style=none&taskId=u900716ff-6e83-4869-87e6-51b339d694c&title=&width=552.6666666666666)<br />可以看到需要调用的函数`get_flag`只有在输入字符串为“MoveBitCTF”时才能成功运行<br />`sui client call --function get_flag --module checkin --package 你的packageid --args "MoveBitCTF" --gas-budget 10000000`<br />将得到的`Transaction Digest`输入即可获得flag
