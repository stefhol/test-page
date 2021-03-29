<script lang="ts">
	import { onMount } from "svelte";
	import Canvas from "./Canvas.svelte";
	import type {Color } from './Color'
	import  {rgba2hex , printColorCSS} from './Color'
	let button:HTMLButtonElement;
	let loading = true
	onMount(()=>{
		loading = false
	})
	let hideControls = false
	let blurValue=0
	let defaultMultiplicator = 0.1
	let defaultHyperspeed = 1
	let hyperspeedSkipValue = 5
	let orbSize = 1
	let starCount = 1000
	let renderOrbs = true
	let color : {
		main:Color,
		second:Color
	} = { 
		main : {
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
	const resetValues = () => {
		blurValue=0
		defaultMultiplicator = 0.1
		defaultHyperspeed = 1
		hyperspeedSkipValue = 5
		orbSize = 1
		starCount = 1000
		renderOrbs = true
	}
	//@ts-ignore
	import Picker from 'svelte-picker';
    

	function colorCallback(rgba:{detail:Color},compName:string) {
		console.log(rgba);
		
		if(compName === "main"){
			color.main = rgba.detail
		}
		if(compName === "second"){
			color.second = rgba.detail
		}
	}
</script>

{#if loading==false}
<Canvas button = {button} 
	blurValue={blurValue}
	defaultMultiplicator ={defaultMultiplicator}
	defaultHyperspeed = {defaultHyperspeed}
	hyperspeedSkipValue = {hyperspeedSkipValue}
	orbSize = {orbSize}
	renderOrbs = {renderOrbs}
	starCount = {starCount}
	color = {color}
/>
{/if}
<main>
	<h1>Time for Hyperspeed</h1>
	<button on:click={()=>{
		hideControls = !hideControls
	}}>{hideControls?"Hide Controls":"Show Controls"}</button>
	{#if hideControls}
	<form>
		<button on:click={e => {
			 e.preventDefault();
			 resetValues()}}>Reset</button>
		<label class = "input-label">Default Multiplicator:
			<input
			type=range
			step=0.1
			min =0 max = 1
			on:change={(e)=>{
			defaultMultiplicator = Number(e.currentTarget.value)}}
			value={defaultMultiplicator}/>
			<input
			type=number
			step=0.1
			min =0 max = 1
			on:change={(e)=>{
			defaultMultiplicator = Number(e.currentTarget.value)}}
			value={defaultMultiplicator}/>
		</label>
		<label class = "input-label">Hyperspeed Multiplicator:
			<input 
			type=range
			min =0 max = 10
			on:change={(e)=>{
			defaultHyperspeed = Number(e.currentTarget.value)}}
			value={defaultHyperspeed}
			/>
			<input 	
			type=number
			min =0 max = 10
			on:change={(e)=>{
			defaultHyperspeed = Number(e.currentTarget.value)}}
			value={defaultHyperspeed}
			/>
				</label>
		<label class = "input-label">Hyperspeed Skip Value:
			<input 
			type=range
			min =0 max = 10
			on:change={(e)=>{
			hyperspeedSkipValue = Number(e.currentTarget.value)}}
			value={hyperspeedSkipValue}
			/>
			<input 
			type=number
			min =0 max = 10
			on:change={(e)=>{
			hyperspeedSkipValue = Number(e.currentTarget.value)}}
			value={hyperspeedSkipValue}
			/>
		</label>
		<label class = "input-label">Particle Size:
			<input
			type=range
			min =0 max = 10
			on:change={(e)=>{
			orbSize = Number(e.currentTarget.value)}}
			value={orbSize}
			/>
			<input
			type=number
			min =0 max = 10
			on:change={(e)=>{
			orbSize = Number(e.currentTarget.value)}}
			value={orbSize}
			/>
		</label>
		<label class = "input-label">Star Count:
			<input
				type=range
				min =500 max = 10000
				on:change={(e)=>{
				starCount = Number(e.currentTarget.value)}}
				value={starCount}/>
				<input
				type=number
				min =500 max = 10000
				on:change={(e)=>{
				starCount = Number(e.currentTarget.value)}}
				value={starCount}/>
		</label>		
		<label class = "input-label">Blur Value:
			<input
				type=range
				min =0 max = 10
				on:change={(e)=>{
				blurValue = Number(e.currentTarget.value)}}
				value={blurValue}/>
				<input
				type=number
				min =0 max = 10
				on:change={(e)=>{
				blurValue = Number(e.currentTarget.value)}}
				value={blurValue}/>
		</label>
		<label class = "input-label">Star Count:
			<input
				type=range
				min =500 max = 10000
				on:change={(e)=>{
				starCount = Number(e.currentTarget.value)}}
				value={starCount}/>
				<input
				type=number
				min =500 max = 10000
				on:change={(e)=>{
				starCount = Number(e.currentTarget.value)}}
				value={starCount}/>
		</label>
		<label class = "input-label">Render Orbs
			<input type=checkbox bind:checked={renderOrbs}>
		</label>
		<div class = "color-picker">
			<div>Main
			<Picker class="" alpha={true} on:colorchange={ rgba => colorCallback(rgba,"main")} startColor={rgba2hex(printColorCSS(color.main,1))}/></div>
			<div>Second
				<Picker class="" alpha={true} on:colorchange={ rgba => colorCallback(rgba,"second")} startColor={rgba2hex(printColorCSS(color.second,1))}/></div>
		</div>
	</form>
	{/if}
	<div><button bind:this={button}>Hover over me</button></div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}
	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
	label{
		color: white;
	}
	label > * {
		width:10rem;
	}
	.color-picker > div {
		    display: inline-block;
			margin: 1rem;
			color:white
	}
</style>


