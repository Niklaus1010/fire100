<script setup>
import { ref, onMounted } from 'vue';

const path = ref('');

function calculatePoints(x, y, width, height) {
    let points = [[x, height / 2]];
    let maxPoints = 10;
    let chunkRange = width / maxPoints;
    for (let i = 0; i < maxPoints; i++) {
    let cx = chunkRange * i + Math.cos(i) * chunkRange;
    let cy = Math.random() * height;
    points.push([cx, cy]);
    }

    points.push([width, height / 2]);

    let d = points.map(point => point.join(','));
    return 'M' + d.join(',');
}

function run() {
    let fps = 25,
    now,
    delta,
    then = Date.now(),
    interval = 1000 / fps,
    iteration = 0;
    function loop() {
    requestAnimationFrame(loop);

    now = Date.now();
    delta = now - then;
    if (delta > interval) {
        then = now - (delta % interval);

      // update stuff
        render(iteration++);
    }
    }
    loop();
}

function render() {
    let d = calculatePoints(0, 0, 500, 80);
    path.value = d;
}

onMounted(() => {
    run();
});

import * as echarts from "echarts";
 // 基於準備好的dom，初始化echarts實例

const chart = ref();
const chart1 = ref();

onMounted(()=>{
// 基於準備好的dom，初始化echarts實例  
var myChart = echarts.init(chart.value);

// 指定圖表的配置項和數據
var option = {
    title: {
    text: '發電量'
    },
    tooltip: {},
    legend: {
    data: ['火力發電量(百萬度)','全國發電量(百萬度)']
    },
    xAxis: {
    data: ['2002', '2003', '2004', '2005', '2006', '2007', '2008', '2009', '2010', '2011', '2012', '2013', '2014', '2015', '2016', '2017', '2018', '2019', '2020', '2021', '2022']
    },
    yAxis: {},
    series: [
    {
        name: '火力發電量(百萬度)',
        type: 'bar',
        data: [150112, 160111, 168976, 176161, 184005, 190320, 185752, 177351, 193729, 198161, 196353, 196659, 204531, 208160, 216423, 232112, 231843, 223403, 230221, 242536, 237492],
    },
    {
        name: '全國發電量(百萬度)',
        type: 'bar',
        data: [198829, 209072, 218397, 227512, 235530, 243117, 238305, 230037, 247059, 252167, 250373, 252341, 259964, 258142, 264108, 270256, 275539, 274192, 280000, 291020, 288154],
    }
    ]
};

//使用剛指定的配置項和數據顯示圖表。
myChart.setOption(option);

var myChart1 = echarts.init(chart1.value);

var option1 = {
    series: [
    {
        type: 'pie',
        data: [
        {
            value: 121037,
            name: '燃煤(50.96%)'
        },
        {
            value: 112006,
            name: '燃氣(47.16%)'
        },
        {
            value: 4449,
            name: '燃油(1.87%)'
        }
        ]
    }
    ]
};
//使用剛指定的配置項和數據顯示圖表
myChart1.setOption(option1);

})

const articles = ref([]);

onMounted(() => {
    fetch('https://tw.news.yahoo.com/latest-news')
    .then(response => response.text())
    .then(html => {
        const parser = new DOMParser();
        const doc = parser.parseFromString(html, 'text/html');
        const articleElements = doc.querySelectorAll('.js-stream-content .StreamComponent');

        const articlesData = Array.from(articleElements).map(element => {
        const titleElement = element.querySelector('.StreamComponent-cardHeadline a');
        const descriptionElement = element.querySelector('.StreamComponent-cardDescription');
        const linkElement = element.querySelector('.StreamComponent-cardHeadline a');

        return {
            title: titleElement ? titleElement.textContent.trim() : '',
            description: descriptionElement ? descriptionElement.innerHTML.trim() : '',
            link: linkElement ? linkElement.href : ''
        };
        });

        articles.value = articlesData;
    })
    .catch(error => {
        console.error('Error fetching news:', error);
    });
});


</script>

<template>   




<div class="body">
<div class="header">
    <div class="photo1">
        <img src="/電塔.png"></img>
    </div>
<div class="electricity">
    <svg ref="svg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 500 200">
        <defs>
        <filter id="f1" x="0" y="0">
            <feGaussianBlur in="SourceGraphic" stdDeviation="5" />
        </filter>
        </defs>
        <g>
        <path :d="path" fill="none" stroke="#42ee77" filter="url(#f1)"></path>
        <path :d="path" fill="none" stroke="#42ee77"></path>
        </g>
    </svg>
</div>
    <div class="photo2">
        <img src="/房子.png"></img>
    </div>
</div>

<div>
    <div class="graph">
    <div ref="chart" style="width: 600px;height:400px;"></div>
    <div ref="chart1" style="width: 600px;height:400px;"></div>
    </div>
</div>

<div class="news-container">
    <div v-for="article in articles" :key="article.link" class="news-card">
        <div class="card-header">
        <a :href="article.link" target="_blank">{{ article.title }}</a>
        </div>
        <div class="card-body">
        <p v-html="article.description"></p>
        </div>
    </div>
    </div>
</div>


</template>

<style  scoped>
.body{
    background: #000;
}

.header{
    justify-content: center;
    display: flex;
}
.electricity {
    max-width: 500px;
    min-height: 80vh;
    margin: 0 auto;
    margin-right: 50px; 
    margin-top: 30px;
    width: 100%;
    position: relative;
    display: flex;
}

svg {
    position: absolute;
    top: 18vh;
    width: 90%;
    left: 50%;
    transform: translate(-50%);
}



.photo1{
    width: 300px;
    height: 400px;
    margin-top: 10px;
    display: flex;
    margin-left: 350px;
}

.photo2{
    width: 300px;
    height: 300px;
    margin-top: 100px;
    display: flex;
    margin-right: 350px;
}

.graph{
    margin-right: 100px;
    display: flex;
    justify-content: center;
}

.news-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
}

.news-card {
  border: 1px solid #ccc;
  border-radius: 4px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.card-header {
  background-color: #f5f5f5;
  padding: 1rem;
}

.card-header a {
  color: #333;
  text-decoration: none;
  font-weight: bold;
}

.card-body {
  padding: 1rem;
}
</style>           