<script lang="ts">
    import {onMount ,afterUpdate} from "svelte"
    import type {Color} from './Color'
    import {printColorCSS} from './Color'
    let canvas:HTMLCanvasElement;
    let width:number;
    let height:number;
    let ctx:CanvasRenderingContext2D;
    let prevTime:number;
    let dpi = window.devicePixelRatio;
    export let blurValue:number;
    export let button: HTMLButtonElement;
    export let defaultMultiplicator:number = 0.1;
    export let defaultHyperspeed:number = 1;
    export let hyperspeedSkipValue:number = 5
    export let orbSize:number = 1
    export let starCount = 1000
    let hyperspeed = false
    export let color:{main:Color,second:Color} = {
        main:{
            r:106,
            g:90,
            b:205,
            a:1
        },
        second:{
            r:30,
            g:70,
            b:90,
            a:1
        }
    }
  
    export let renderOrbs = true
        onMount(()=> {
            ctx = canvas.getContext("2d")
            requestAnimationFrame(init)
            handleResize()
            stars = makeStars(starCount)
        button.onmouseenter =  ()=>{
            hyperspeed = true
        }
        button.onmouseleave =  ()=>{
            hyperspeed = false
        }
    })
    const init = time => {
        prevTime = time
        requestAnimationFrame(localTick)
    }
    let skip = 0
    const localTick = time => {
        let elapsed = time - prevTime
        prevTime = time;
        moveStars(elapsed *( hyperspeed ? defaultHyperspeed: defaultMultiplicator));

        if(hyperspeed){
            if(skip <  hyperspeedSkipValue){
                skip+=1
            }
            else{
                clear()
                skip =0
            }
        }
        else{
            clear();
        }
        const cx = width/2
        const cy = height/2

        stars.forEach(star => {
            const x = cx + star.x/(star.z * 0.001);
            const y = cy + star.y/(star.z * 0.001);
            if(x<0 || x >= width || y< 0 || y>=height){
            
            } 
            else {
                const d = star.z/1000.0
                const b = 1-d*d
                putPixel(x,y,b)
            }
        })
        requestAnimationFrame(localTick)
    }
    const handleResize = () => {
        width = document.body.clientHeight *dpi;
        height = document.body.clientWidth * dpi;
        try{
            canvas.width = width;
            canvas.height = height;
        }catch(err){}
    }
    const makeStars = (count:number) => {
        const winX = width
        const winY = height
        console.table({winX,winY})
        const out = [];
        for (let i = 0; i < count; i++) {
            out.push({
                x:Math.random()*winX-winX/2,
                y:Math.random()*winY-winY/2,
                z:Math.random()*1000
            })
        }
        return out;      
    }
    let stars:{x:number,z:number,y:number}[];
    const clear = () => {
        ctx.fillStyle = "black";
        ctx.fillRect(0,0,canvas.width,canvas.height)
    }
    const putPixel = (x:number,y:number,brightness:number) => {
        const intensity = brightness*2;
        if(renderOrbs){
            var radgrad = ctx.createRadialGradient(x,y,0,x,y,360);
            radgrad.addColorStop(0, printColorCSS(color.main,intensity))
            radgrad.addColorStop(0.8, printColorCSS(color.second,intensity))
            radgrad.addColorStop(1, "black")
            ctx.beginPath();
            ctx.fillStyle = radgrad
            ctx.arc(x, y, orbSize, 0, Math.PI*2, true); 
            ctx.fill();
            ctx.closePath()
        }
        else{
            let rgb = printColorCSS(color.main,intensity)
            ctx.fillStyle = rgb
            ctx.fillRect(x,y,orbSize,orbSize) 
        }
        
    }
    const moveStars = (distance:number) => {
        stars = stars.map(star => {
            star.z -= distance;
            while (star.z <= 1){
                star.z += 1000;
            }
            return star;
        })
    }

    afterUpdate(()=> {
        if (starCount !== 1000) {
            stars = makeStars(starCount)
        }
        blurValueString =`${blurValue?blurValue:1}px`
        hyperspeed = false
    })
    let blurValueString = `${blurValue?blurValue:1}px`
</script>
<canvas id="background" bind:this={canvas} style="--blur-value: {blurValueString}">
</canvas>
<svelte:window on:resize={handleResize}/>
<style>
    canvas#background{
        width: 100%;
        height: 100%;
        padding: 0;
        margin: 0;
        position: fixed;
        z-index: -1;
        filter:blur(var(--blur-value))
    }
</style>