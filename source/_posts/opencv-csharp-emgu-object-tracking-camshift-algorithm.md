title: 'OpenCV (C# Emgu) 物件追蹤程式，使用 Camshift 演算法'
tags:
  - 'C#'
  - Emgu
  - 影像處理
id: 12
categories:
  - 'C#'
  - 影像處理
date: 2013-05-26 13:50:26
---

這學期擔任影像處理助教，所以要出作業給學弟妹練習一下 **OpenCV**
會使用 **Emgu** 不外乎是**環境好建置**，只要把將範例專案做好基本上下載回去能馬上使用
可以專心在撰寫功能不需要去建置環境，雖然說建置環境對一個工程師也是一項基本功啦...

<!--more-->

首先先來搞清楚一下整個物件追蹤的來龍去脈...

## 整個流程

1\. 畫面上選取要追蹤的區域這裡我稱為 **物件 (Object)**，也就是要被追蹤的東東
2\. 計算出物件的 **直方圖 ([Histogram](http://en.wikipedia.org/wiki/Histogram))**
3\. 將物件的直方圖與輸入畫面進行 **反向投影([Backproject](http://docs.opencv.org/doc/tutorials/imgproc/histograms/back_projection/back_projection.html))** 計算
4\. 透過 **物件追蹤演算法(Camshift)** 快速地框出反向投影計算出的結果
5\. 回傳追蹤區域
於是一直重複 3~5 的過程，我們的物件追蹤程式就大功告成了！

&nbsp;

## 範例專案 [ObjectTracking.cs](https://github.com/imZack/Digital-Image-Processing-2/blob/master/ObjectTracking.cs)

### 先宣告所有變數

{% codeblock lang:csharp %}        public Image&lt;hsv, byte=""&gt; hsv;
        public Image&lt;gray, byte=""&gt; hue;
        public Image&lt;gray, byte=""&gt; mask;
        public Image&lt;gray, byte=""&gt; backproject;
        public DenseHistogram hist;
        private Rectangle trackingWindow;
        private MCvConnectedComp trackcomp;
        private MCvBox2D trackbox;{% endcodeblock %}

### 建構子

{% codeblock %}        public ObjectTracking(Image&lt;Bgr, Byte&gt; image, Rectangle ROI)
        {
            // Initialize parameters
            trackbox = new MCvBox2D();
            trackcomp = new MCvConnectedComp();
            hue = new Image&lt;Gray, byte&gt;(image.Width, image.Height);
            hue._EqualizeHist();
            mask = new Image&lt;Gray, byte&gt;(image.Width, image.Height);
            hist = new DenseHistogram(30, new RangeF(0, 180));
            backproject = new Image&lt;Gray, byte&gt;(image.Width, image.Height);

            // Assign Object's ROI from source image.
            trackingWindow = ROI;

            // Producing Object's hist
            CalObjectHist(image);
        }{% endcodeblock %}

### 更新 HUE 畫面

{% codeblock %}        private void UpdateHue(Image&lt;Bgr, Byte&gt; image)
        {
            // release previous image memory
            if (hsv != null)    hsv.Dispose();
            hsv = image.Convert&lt;Hsv, Byte&gt;();

            // Drop low saturation pixels
            mask = hsv.Split()[1].ThresholdBinary(new Gray(60), new Gray(255));
            CvInvoke.cvInRangeS(hsv, new MCvScalar(0, 30, Math.Min(10, 255), 0),
                new MCvScalar(180, 256, Math.Max(10, 255), 0), mask);

            // Get Hue
            hue = hsv.Split()[0];
        }{% endcodeblock %}

### 計算物件直方圖

{% codeblock %}        private void CalObjectHist(Image&lt;Bgr, Byte&gt; image)
        {
            UpdateHue(image);

            // Set tracking object's ROI
            hue.ROI = trackingWindow;
            mask.ROI = trackingWindow;
            hist.Calculate(new Image&lt;Gray, Byte&gt;[] { hue }, false, mask);

            // Scale Historgram
            float max=0, min=0, scale=0;
            int[] minLocations, maxLocations;
            hist.MinMax(out min, out max, out minLocations, out maxLocations);
            if (max != 0)
            {
                scale = 255 / max;
            }
            CvInvoke.cvConvertScale(hist.MCvHistogram.bins, hist.MCvHistogram.bins, scale, 0);

            // Clear ROI
            hue.ROI = System.Drawing.Rectangle.Empty;
            mask.ROI = System.Drawing.Rectangle.Empty;

            // Now we have Object's Histogram, called hist.
        }{% endcodeblock %}

### 執行追蹤

{% codeblock %}        public Rectangle Tracking(Image&lt;Bgr, Byte&gt; image)
        {
            UpdateHue(image);

            // Calucate BackProject
            backproject = hist.BackProject(new Image&lt;Gray, Byte&gt;[] { hue });

            // Apply mask
            backproject._And(mask);

            // Tracking windows empty means camshift lost bounding-box last time
            // here we give camshift a new start window from 0,0 (you could change it)
            if (trackingWindow.IsEmpty || trackingWindow.Width==0 || trackingWindow.Height==0)
            {
                trackingWindow = new Rectangle(0, 0, 100, 100);
            }
            CvInvoke.cvCamShift(backproject, trackingWindow,
                new MCvTermCriteria(10, 1), out trackcomp, out trackbox);

            // update tracking window
            trackingWindow = trackcomp.rect;

            return trackingWindow;
        }{% endcodeblock %}
<iframe width="640" height="480" src="https://www.youtube-nocookie.com/embed/RavZvuspQks?rel=0" frameborder="0" allowfullscreen=""></iframe>

完整範例專案放置於 [Github ](https://github.com/imZack/Digital-Image-Processing-2 "Digital-Image-Processing-2")