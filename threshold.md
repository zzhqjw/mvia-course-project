#include "opencv2/core/core.hpp"
#include "opencv2/imgproc/imgproc.hpp"
#include "opencv2/highgui/highgui.hpp"
#include <string>

using namespace cv;

int main()
{
  
    cv::Mat image = cv::imread("lena.bmp",cv::IMREAD_GRAYSCALE);
    cv::Mat binary,binary_inv;
    cv::threshold(image,binary,125,255,cv::THRESH_BINARY);
    cv::adaptiveThreshold(image,binary_inv, 255,cv::ADAPTIVE_THRESH_MEAN_C, cv::THRESH_BINARY, 25,10);
    imshow("adaptthreshold", binary_inv);
    imwrite("adaptbinary.jpg", binary_inv);
    imshow("threshold", binary);
    imwrite("binary.jpg", binary);
    waitKey();
    return 0;
}
