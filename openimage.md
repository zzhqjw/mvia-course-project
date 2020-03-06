#include<opencv2/core.hpp>
#include<opencv2/imgcodecs.hpp>
#include<opencv2/highgui.hpp>

#include<iostream>
#include<string>

int main(int argc, char*argv[]){
 if(argc <2){
	std::cout<<"Usage: ...."<< std::endl;
return 0;
  }
  std::string image_name(argv[1]);
  cv::Mat image = cv::imread(image_name,cv::IMREAD_COLOR);
  cv::namedWindow("Display Window",cv::WINDOW_AUTOSIZE);
  cv::imshow("Display Window",image);
  cv::waitKey(0);
  return 0;
}
