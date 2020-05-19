#include <opencv2/opencv.hpp>
#include <iostream>
using namespace cv;
using namespace std;
bool TestReadData(cv::String filename , std::vector<cv::Point2i> &ptdata);
int main(int argc, char** argv)
{       // std::string image_name(argv[1]);
        // Mat src = imread(image_name,cv::IMREAD_COLOR);
	//Mat src_gray, binary,blur_image;
	//Mat draw= src.clone();
  	//Mat drawing(src.size(),CV_8UC3,Scalar(255,255,255));  
	//if (src.empty()) {
	//	printf("Could not load image...");
	//	return -1;
	//}
	//imshow("Input Image", src);
	vector<Point2i>ptdata;
	String filename(argv[1]);
	TestReadData(filename,ptdata);
	Mat src(800,800,CV_8UC3,Scalar(255,255,255));
	Mat draw= src.clone();
	Mat drawing(src.size(),CV_8UC3,Scalar(255,255,255));  
	Mat src_gray, binary,blur_image;
	for (int i = 0; i<ptdata.size(); i++) 
	{
        if(i+1==ptdata.size())
       {break;}
        Point pt1(ptdata[i]);
        Point pt2(ptdata[i+1]);    
        line(src,pt1,pt2,Scalar(0,0,0),2); //  线条宽度设置为2
        }
	imshow("src",src);
	//二值化
	cvtColor(src, src_gray, COLOR_BGR2GRAY);
        blur(src_gray, blur_image, Size(3, 3)); 
	threshold(blur_image, binary, 0, 255, THRESH_BINARY | THRESH_OTSU);   
	binary = ~binary;

        vector<vector<Point>>contours;
        vector<Vec4i>hierarchy;
       //寻找图像轮廓
       // findContours( binary, contours, hierarchy, RETR_TREE, CHAIN_APPROX_SIMPLE, Point());
        findContours( binary, contours, hierarchy, RETR_EXTERNAL, CHAIN_APPROX_SIMPLE, Point());
       //发现凸包
        vector<vector<Point>>hull(contours.size());
        for (int i = 0; i < contours.size(); i++)
        {
            convexHull(Mat(contours[i]), hull[i], false);
        }
        //用于存放折线点集
        vector<vector<Point>> contours_poly(contours.size());
	//存放矩形框
	vector<Rect> boundRect(contours.size());
        //存放圆心和半径
	vector<Point2f>center(contours.size());
	vector<float>radius(contours.size());
        //绘制轮廓和凸包
      
        for (int i = 0; i < contours.size(); i++)
        {          
              int epsilon = 0.01 * arcLength(contours[i], true);
            approxPolyDP(hull[i],  contours_poly[i], epsilon, true);	 
          //  drawContours(drawing, contours_poly, i, Scalar(0, 0, 255), 3, 8, vector<Vec4i>(), 0, Point());
             minEnclosingCircle(contours_poly[i], center[i], radius[i]);
             boundRect[i] = boundingRect(Mat(contours_poly[i]));
           //  drawContours(drawing, contours_poly, i, Scalar(0, 0, 255), 2, 8, Mat(), 0, Point());//dst必须先初始化 
          
        }
	int dx,dy,tan;
		if (contours_poly[0].size() == 2)
	{
 	dx =abs(contours_poly[0][0].x-contours_poly[0][1].x);
 	dy =abs(contours_poly[0][0].y-contours_poly[0][1].y);
 	tan = dy/dx;	
		if (tan>1)
	{
        line(drawing,Point(contours_poly[0][0].x,contours_poly[0][0].y),Point(contours_poly[0][1].x,contours_poly[0][1].y),Scalar(255,0,0),2);
                cout << "这是垂直线：" << contours_poly[0].size() << endl;
	}
		else
 	{	    
	line(drawing,Point(contours_poly[0][0].x,contours_poly[0][0].y),Point(contours_poly[0][1].x,contours_poly[0][1].y),Scalar(255,0,0),2);
                cout << "这是水平线：" << contours_poly[0].size()  << endl;
	}
	}
                if (contours_poly[0].size() == 3)
		{	
	drawContours(drawing, contours_poly, 0, Scalar(255, 0, 0), 2, 8, vector<Vec4i>(), 0, Point());
              cout << "这是三角形：" << contours_poly[0].size() << endl;
		}
                if (contours_poly[0].size() == 4)
		{	
	rectangle(drawing, boundRect[0].tl(), boundRect[0].br(), Scalar(255, 0, 0), 2, 8, 0);
              cout << "这是矩形：" << contours_poly[0].size() << endl;
		}
                if (contours_poly[0].size() > 4)
 		{	
        circle(drawing, center[0], (int)radius[0], Scalar(255, 0, 0), 2, 8, 0);
              cout << "这是圆形：" << contours_poly[0].size() << endl;
		}
cout << "----------vector<vector<Point>> contours_poly------------" << endl;
	for (size_t i = 0; i < contours_poly.size(); i++)
	{
		cout << "第" << i << "行：";
		for (size_t j = 0; j < contours_poly[i].size(); j++)
		{
			cout<<contours_poly[i][j];
		}
		cout << endl;
	}
	 cout << endl;
        imshow("drawing", drawing);
	waitKey(0);
	return 0;
}
bool TestReadData(cv::String filename , std::vector<cv::Point2i> &ptdata)
{
  cv::FileStorage fs;
  if (!fs.open(filename, cv::FileStorage::READ)) {
    std::cout << "Cannot open file: " << filename << std::endl;
    return false;
  }
  cv::FileNode data_node = fs["data"];
  for (cv::FileNodeIterator it = data_node.begin(); it != data_node.end(); ) {
    int a, b;
    *it++ >> a;
    *it++ >> b;
    ptdata.push_back(cv::Point2i(a, b));
  }

  fs.release();
  return true;
}
