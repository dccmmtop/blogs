## Rails中使用图表(ChartJs)显示数据

利用图表将数据呈现出来，更直观，在Rails中有一个gem包叫做[chartjs-ror](https://github.com/airblade/chartjs-ror)

使用方法非常简单。按照github上装好以后，使用方法如下：

* 曲线图

  在view中要显示图表的地方插入`<%= line_chart @data,@options%>`即可

  `@data`和`@options`从哪里来？？ 当然从controller中来

  在controller中

  ```ruby
  @data ={
        labels: @time, # 横轴数据
        datasets:[
          {   
            hidden: true # 默认不显示，可以手动点击，将曲线显示出来
            label: 'ETC盈利率', #该条曲线的意思
            borderColor: "rgba( 112, 151, 152,1)", # 曲线的颜色 和透明度
            data: etc_data # 该条曲线的数据
          },  
          {   
            label: 'BTC盈利率',
            borderColor: "rgba( 131 ,111 ,255,1)",
            data: btc_data
          },  
          {   
            label: 'ETH盈利率',
            borderColor: "rgba(0 ,255, 0,1)",
            data: eth_data
          }
        ]   
      }   
  width = @time.size / 47 * 1797 # 计算图表的宽度，使其比例合适
  width = 1797 if width < 1797
  @options = {width:"#{width.to_i}px",height:'800px'}# 这里指定生成图标的宽度和高度

  ```

  ​

在view中

```ruby
<div class = "col-md-10" style = "overflow-x:scroll"> # 当图表横轴数据过多时，出现滚动条
  <div class="box" style="width:@options[:width];height:@options[:height]"> # 放图表的容器的大小应该与图表一致
      <%= line_chart @data,@options %>
  </div>
</div>
```

就这了，里面还有很多属性和图表样式，请参考[文档](http://www.bootcss.com/p/chart.js/docs/)。