---
layout: mypost
title: PyTorch实现3D Sobel算子
categories: [python, medical image, PyTorch]
---

```python
class sobel3d(nn.Module):
    def __init__(self, channel=15):
        super(sobel3d, self).__init__()
        self.channel = channel
        self.kernel_z = torch.tensor(
            [[[1, 2, 1],
            [2, 4, 2],
            [1, 2, 1]],
            
            [[0, 0, 0],
            [0, 0, 0],
            [0, 0, 0]],
            
            [[-1, -2, -1],
            [-2, -4, -2],
            [-1, -2, -1]]], requires_grad=False
        )
        self.kernel_x = torch.tensor(
            [[[1, 0, -1],
            [2, 0, -2],
            [1, 0, -1]],
            
            [[2, 0, -2],
            [4, 0, -4],
            [2, 0, -2]],
            
            [[1, 0, -1],
            [2, 0, -2],
            [1, 0, -1]]], requires_grad=False
        )
        self.kernel_y = torch.tensor(
            [[[1, 2, 1],
            [0, 0, 0],
            [-1, -2, -1]],
            
            [[2, 4, 2],
            [0, 0, 0],
            [-2, -4, -2]],
            
            [[1, 2, 1],
            [0, 0, 0],
            [-1, -2, -1]]], requires_grad=False
        )
        self.kernel_z = self.kernel_z.unsqueeze(0).expand([channel, 1, 3, 3, 3]).to(torch.float32).cuda()
        self.kernel_x = self.kernel_x.unsqueeze(0).expand([channel, 1, 3, 3, 3]).to(torch.float32).cuda()
        self.kernel_y = self.kernel_y.unsqueeze(0).expand([channel, 1, 3, 3, 3]).to(torch.float32).cuda()
        
    def forward(self, x):
        y_z = F.conv3d(x, self.kernel_z, stride=1, padding=1, groups=15, bias=None)
        y_x = F.conv3d(x, self.kernel_x, stride=1, padding=1, groups=15, bias=None)
        y_y = F.conv3d(x, self.kernel_y, stride=1, padding=1, groups=15, bias=None)
        y = torch.mul(y_z, y_z) + torch.mul(y_x, y_x) + torch.mul(y_y, y_y)
        y = torch.sqrt(y)
        return y
```
