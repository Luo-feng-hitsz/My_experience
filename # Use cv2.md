# Use cv2-python
## install
*     pip install opencv-python
## Read Image(BGR)
```python
import cv2

#彩色读取
img=cv2.imread('picture.jpg',cv2.IMREAD_COLOR)
img1=cv2.imread('picture.jpg',1)

#灰度读取
img2=cv2.imread('picture.jpg',cv2.IMREAD_GRAYSCALE)
img3=cv2.imread('picture.jpg',0)
```
## Show Image
```python
cv2.imshow('cat',img2)
cv2.waitKey(0)  #0一直显示，直到有键盘输入。也可以是其他数字，毫秒级
```
## Save Image
```python
cv2.imwrite('picture.jpg',img)
```
## Add words
```python
new_img = cv2.putText(
    img=img1,
    text="Kira",  #文本
    org=(0, 50),    #字体开始写的左上角位置
    fontFace=cv2.FONT_HERSHEY_SIMPLEX, #字体
    fontScale=1, #字体大小
    color=(255, 0, 0),   #颜色
    thickness=2  #字体粗细
)
```
## Exchange of color
```python
Color_image=cv2.cvtColor(Img_1,cv2.COLOR_RGB2GRAY)   
#cv2.COLOR_X2Y 其中X,Y = RGB, BGR, GRAY, HSV, YCrCb, XYZ, Lab, Luv, HLS
```
## Copy and Flip
```python
new_img = img1.copy()
new_img = cv2.flip(img1, flipCode=-1)
```
## Change every pixel of the image
```python
for x in np.nditer(img, op_flags=['readwrite']):
    x[...] = img_map[x]
```
        