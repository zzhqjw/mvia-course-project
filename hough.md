#include "opencv2/core/core.hpp"
#include "opencv2/highgui/highgui.hpp"
#include "opencv2/imgproc/imgproc.hpp"
#include <iostream>
using namespace cv;
using namespace std;
int main( int argc, char** argv )
{
 	Mat src, edge,color_edge;
	src=imread("cir.jpg");
	if( !src.data )  
		return -1;  
	Canny(src,edge,50,200,3);
	cvtColor( edge, color_edge, CV_GRAY2BGR );
	vector<Vec4i> lines;
    HoughLinesP(edge, lines, 3, CV_PI/180, 50, 200, 30 );
    for( size_t i = 0; i < lines.size(); i++ )
    {
            //绘制直线
        Vec4i l = lines[i];
        line( src, Point(l[0], l[1]), Point(l[2], l[3]), Scalar(0,0,255), 3,CV_AA);
    }
   vector<Vec3f> circles;
        HoughCircles(edge, circles, HOUGH_GRADIENT, 1, edge.rows/5, 150, 30, 100, 0);
        for (size_t i = 0; i < circles.size(); i++)
        {
            Point center(cvRound(circles[i][0]), cvRound(circles[i][1]));
            int radius = cvRound(circles[i][2]);
            //绘制圆心十字
        line(src,Point(circles[i][0]-20.0,circles[i][1]),Point(circles[i][0]+20.0,circles[i][1]), Scalar(0, 0, 255),3,CV_AA);
	
	line(src,Point(circles[i][0],circles[i][1]-20.0),Point(circles[i][0],circles[i][1]+20.0), Scalar(0,0, 255),3,CV_AA);
            //绘制圆轮廓
            circle(src, center, radius, Scalar(0, 0, 255), 3, 8, 0);
        }

    namedWindow( "Output", CV_WINDOW_NORMAL );
    imshow("Output",src);
    waitKey(0);
    return 0;
}

