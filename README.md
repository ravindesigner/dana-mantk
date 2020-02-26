# yary sayaraka
sayara
<!DOCTYPE html>
<html>
    <head>
        <title>یاری سەیارە</title>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.7.2/p5.js"></script>
   <style>
   body {
    margin: 0;
    color: #fff;
    overflow: hidden;
    
}

canvas
{
    position: absolute;
}

.score
{
    position: absolute;
    font-size: 20px;
    left: 15px;
    font-weight: bold;
}

.gm
{
    position: absolute;
    height: 100vh;
    width: 100vw;
    background: rgba(0,0,0,0.3);
    display: flex;
    justify-content: center;
    align-items: center;
    display: none;
}

.restart
{
    height: 30px;
    width: 100px;
    background: #000;
    color: #fff;
    border: none;
    border-radius: 5px;
}

#load
{
    position: absolute;
    width: 100vw;
    text-align: center;
    top: 50%;
    transform: translateY(-50%);
    color: #000;
}



.toast
{
    position: absolute;
    z-index: 10;
    height: 50px;
    width: 100vw;
    background: #212121;
    color: #fff;
    display: flex;
    justify-content: left;
    align-items: center;
    font-size: 15px;
    padding: 0 0 0 10px;
    box-sizing: border-box;
    bottom: -50px;
    transition: 0.5s;
}
   </style>
   <script>
   let bg;
let w = innerWidth, h = innerHeight;
let carimg = [];
let car;
let objects = [];
let objImg;
let scores = 0;
let btn, div, p, t;



let carlinks = [
    'https://i.ibb.co/PT2xxVp/car04.png',
    'https://i.ibb.co/kgYj6f8/car02.png',
    'https://i.ibb.co/NSDsPDM/car01.png',
    'https://i.ibb.co/T8MPmfy/car03.png'
];



function preload(){
    img = loadImage('https://cors-anywhere.herokuapp.com/' + 'https://i.ibb.co/cLk8jZD/images-1.jpg');
    bike = loadImage('https://i.ibb.co/GT9pSJp/bike-motocross.png');
    tire = loadImage('https://i.ibb.co/XFgpdSt/bike-tire.png');
    
    
    for (let i = 0; i < carlinks.length; i++){
        carimg[i] = loadImage(carlinks[i]);
    }
    
    objImg = loadImage('https://i.ibb.co/tXLCpN8/Crate.png');
    
    
}

function setup(){
    createCanvas(w, h);
    
    // Make a new Background
    
    
    bg = new InitInfiniteBG({
        image : img,
        speed : 5,
        direction: 'left',
        activeStatus : true
    });
    
    // Initialize Background
    
    bg.init();
    
  
    
    car = new Car(20, h - 180, 110, 60, carimg);
    
    let c = document.querySelector('canvas');
    
    c.addEventListener('click', e => {
        car.jump();
    });
    
    
    
    setTimeout(() => {
        t = setInterval(addObjects, 2000);//addObjects();
    }, 9000);
    
    function addObjects(){
        let item = new Obj(w, h - 140, 65, 65, objImg);
        objects.push(item);
    }
    
    
    createScore();
    createWindow();
    
    btn = document.getElementsByClassName('restart')[0];
    div = document.getElementsByClassName('gm')[0];
    
    p = document.getElementsByClassName('score')[0];
    
    
    btn.addEventListener('click', e => {
        div.style.display = 'none';
        objects.splice(0, objects.length);
        scores = 0;
        p.innerText = 'SCORe : ' + scores;
         t = setInterval(addObjects, 2000);//addObjects();
        loop();
    }); 
    
    
    makeToast('Hello There!', 2000);
    makeToast('Click to jump! ', 6000);
    makeToast('Objects are coming! Be Ready! Good Luck! - Arb', 9000);

}






function draw(){

    // Update Baclground
    
    bg.update();
    
    
    //mage(car, 20, h - 165, 120, 100);
    
    
    
    
    for (i of objects){
        i.draw();
        i.update();
    }
    
    
    for (let i = 0; i < objects.length; i++){
        if (objects[i].x <= -objects[i].w){
            objects.splice(i, 1);
            scores++;
            p.innerText = 'SCORE : ' + scores;
            
            
        }
    }
    
    
    car.draw();
    car.update();
    
    for (let i = 0; i < objects.length; i++){
        if (collision(car, objects[i])){
            noLoop();
            div.style.display = 'flex';
            clearInterval(t);
        }
    }
    
    
    
}

function collision(a, b) {
  return a.x + 5 < b.x + b.w  && 
         a.x + a.w > b.x + 5  && 
         a.y < b.y + b.h && 
         a.y + a.h > b.y + 10;   
}


class Car{
    constructor(x, y, w, h, img){
        this.x = x;
        this.y = y;
        this.w = w;
        this.h = h;
        this.speed = 3;
        this.img = img;
        this.carspeed = 0.3;
        this.index = 0;
        this.velY = 0;
        this.gravity = 0.25;
        this.grounded = false;
    }
    
    draw(){
    
        let i = floor(this.index) % this.img.length;
        image(this.img[i], this.x, this.y, this.w, this.h);
        
        this.index += this.carspeed;
        
    }
    
    
    jump(){
        
        if (this.grounded == true){
            this.velY = -3 * 3;
            this.grounded = false;
        }
        
    }
    
    update(){
        
        this.velY += this.gravity;
        this.y += this.velY;
        
        if (this.y > h - 135){
            this.y = h - 135;
            this.grounded = true;
        }
    }
    
    
    
    
    
}



class Obj{
    constructor(x, y, w, h, img){
        this.x = x;
        this.y = y;
        this.h = h;
        this.w = w;
        this.speed = 5;
        this.img = img;
        
    }
    
    draw(){
        
        image(this.img, this.x, this.y, this.w, this.h);
        
        
    }
    
    update(){
        
        
        if (this.x > -this.w){
            this.x -= this.speed;
            
        }
        
        
    }
}


function createScore(){
    let b = document.getElementsByTagName('body')[0];
    let p = document.createElement('p');
    p.classList.add('score');
    p.innerText = 'SCORE : ' + scores;
    b.appendChild(p);
}

function createWindow(){
    let b = document.getElementsByTagName('body')[0];
    let div = document.createElement('div');
    div.classList.add('gm');
    let btn = document.createElement('button');
    btn.innerText = 'RESTART';
    btn.classList.add('restart');
    div.appendChild(btn);
    b.appendChild(div);
}

function makeToast(text, delay){
    let body = document.getElementsByTagName('body')[0];
    let div = document.createElement('div');
    div.classList.add('toast');
    div.innerText = text;
    body.appendChild(div);
    
    setTimeout(() => {
        div.style.bottom = '0';
        
        setTimeout(() => {
            div.style.bottom = '-50px';
        }, 2000);
        
        
    }, delay);
}


class InfiniteScrollingBackground{
    constructor(x, y, w, h, img, speed, dir, active){
        this.x = x;
        this.y = y;
        this.h = h;
        this.w = w;
        this.img = img;
        this.speed = speed;
        this.dir = dir;
        this.active = active;
    }
    
    draw(){
        image(this.img, this.x, this.y, this.w, this.h);
    }
    
    update(){
    
        this.draw();
    
        if (this.active == true){
    
        
        if (this.dir == 'right'){
            if (this.x < innerWidth * 2){
                this.x += this.speed;
            }
            else
            {
                this.x = -innerWidth * 2;
            }
        }
        
        if (this.dir == 'left'){
            if (this.x > -innerWidth * 2){
                this.x -= this.speed;
            }
            else
            {
                this.x = innerWidth * 2;
            }
        }
        
        
        }
        
    }
    
}


class InitInfiniteBG{
    
    constructor(obj){
        this.img = obj.image;
        this.speed = obj.speed;
        this.dir = obj.direction;
        this.active = obj.activeStatus;
    }
    
    init(){
        
        this.bg1 = new InfiniteScrollingBackground(-innerWidth, 0, innerWidth * 2, innerHeight, this.img, this.speed, this.dir, this.active);
        
        this.bg2 = new InfiniteScrollingBackground(innerWidth, 0, (innerWidth * 2) + 5, innerHeight, this.img, this.speed, this.dir, this.active);
        
    }
    
    update(){
        this.bg1.draw();
        this.bg1.update();
        this.bg2.draw();
        this.bg2.update();
    }
    
    
}
   </script>
   </head>
    <body>
        
        <p id="load">چاوەڕوان بە....</p>
        
    </body>
</html>
