vic blog
====
二分查找算法：

    int BinarySearch(int s[],int length,int key)
    {
      int low,high,middle;
      low = 0;
      high= length - 1;
      while(low <= high){
        middle = low + (high-low)>>1;
        if(s[middle] == key)
            return middle;
        else if(s[middle] > key)
            high = middle - 1;
        else
            low = middle + 1;
      }
      return -1;
    }
