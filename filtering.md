#include "opencv2/core/core.hpp"
#include "opencv2/imgproc/imgproc.hpp"
#include "opencv2/highgui/highgui.hpp"
#include <string>
using namespace std;
using namespace cv;
 
//边缘检测
int main()
{
    Mat img = imread("lena.bmp");
    imshow("原始图", img);
    Mat grad_x, grad_y;
    Mat abs_grad_x, abs_grad_y, dst1;
    //sobel算法
    //求x方向梯度
    Sobel(img, grad_x, CV_16S, 1, 0, 3, 1, 1,BORDER_DEFAULT);
    convertScaleAbs(grad_x, abs_grad_x);
    //求y方向梯度
    Sobel(img, grad_y,CV_16S,0, 1,3, 1, 1, BORDER_DEFAULT);
    convertScaleAbs(grad_y,abs_grad_y);  
    //合并梯度
    addWeighted(abs_grad_x, 0.5, abs_grad_y, 0.5, 0, dst1);
    imshow("sobel效果图", dst1);
    //拉普拉斯算法
    Mat gray, dst2,abs_dst2;
    //高斯滤波消除噪声
    GaussianBlur(img, img, Size(3, 3), 0, 0, BORDER_DEFAULT);
    //转换为灰度图
    cvtColor(img, gray, COLOR_RGB2GRAY);
    //使用Laplace函数
    Laplacian(gray, dst2, CV_16S, 3, 1, 0, BORDER_DEFAULT);
    //计算绝对值，并将结果转为8位
    convertScaleAbs(dst2, abs_dst2);
    imshow("laplace效果图", abs_dst2);    
   //canny算法
    Mat DstPic, edge;
    //创建与src同类型和同大小的矩阵
    DstPic.create(img.size(), img.type()); 
    //先使用3*3内核来降噪
    blur(gray, edge, Size(3, 3));
    //运行canny算子
    Canny(edge, edge, 3, 9, 3);
    imshow("canny效果图", edge);
waitKey(0);
 }


