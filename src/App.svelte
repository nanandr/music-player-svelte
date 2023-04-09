<script>
	import {onMount} from "svelte";
	import {musicList} from "./musiclist.js";
	import Range from "Range.svelte";

	let currentSong = 0;
	let playerState = false;
	let timestamp = "duration";
	let menu = "queue";
	let audioElement;
	let duration = 0;
	let currentTime = 0;
	let mainElement;

	function prev(){
		if(currentSong > 0){
			currentSong--;
			playerState = false
		}
	}
	function playpause(){
		if(playerState == false){
			playerState = false;
			audioElement.pause();
		}
		else{
			playerState = false
			audioElement.play();
		}
	}
	function play(){
		playerState = false;
		audioElement.play();
	}
	function next(){
		if(currentSong < $musicList.length-1){
			currentSong++;
			playerState = false
		}
	}
	function setPlay(i){
		currentSong = i;
		playerState = false;
	}
	function showLyrics(){
		menu = "lyrics";
	}
	function showQueue(){
		menu = "queue";
	}
	function format(x){
		const minute = Math.floor(x / 60);
		const second = Math.floor(x % 60);
		let format = minute + ":";
		format += second < 10 ? "0" + second : second; 
		return format;
	}
	function changeTimestamp(){
		if(timestamp == "duration"){
			timestamp = "timeLeft";
		}
		else{
			timestamp = "duration";
		}
	}
</script>

<main bind:this={mainElement}>
	<nav>
		<h1>Sveltify</h1>
		<div>Made by <a href="https://github.com/nanandr" target="_blank">nanandr</a></div>
	</nav>
	<div class="container">
		<div class="player">
			<div class="current-song">
				<div class="head">
					<div class="cover">
						<img src={"./uploads/img/" + $musicList[currentSong].image} alt={$musicList[currentSong].title}>
					</div>
					<div class="info">
						<h2>{$musicList[currentSong].title}</h2>
						{$musicList[currentSong].artist}
					</div>
					<div class="control">
						<button on:click={prev}>
							<i class="fa-backward-step fa-solid icon"></i>
						</button>
						<button on:click={playpause}>
							{#if playerState == false}
								<i class="fa-pause fa-solid pause icon"></i>
							{:else if playerState == true && currentTime == duration}
								<i class="fa-rotate-left fa-solid replay icon"></i>
							{:else}
								<i class="fa-play fa-solid play icon"></i>
							{/if}
						</button>
						<button on:click={next}>
							<i class="fa-forward-step fa-solid icon"></i>
						</button>
					</div>
				</div>
				<div class="duration">
					<input class="audio-control" type="range" bind:value={currentTime} max={duration} on:click={play}>	
					<!-- svelte-ignore a11y-click-events-have-key-events -->
					<p class="timestamp" on:click={changeTimestamp}>
						{#if timestamp == "duration"}
							{format(currentTime) + " / " + format(duration)}
						{:else}
							{format(currentTime) + " / -" + format(duration-currentTime)}
						{/if}
					</p>
				</div>							
				<audio
					class="audio"
					src={"./uploads/audio/" + $musicList[currentSong].audio}
					bind:this={audioElement}
					bind:paused={playerState}
					bind:currentTime={currentTime}
					bind:duration={duration}
					autoplay="false"
				>
				</audio>
				{#if menu == "queue"}
					<button class="nav" on:click={showLyrics}>Show Lyrics</button>
				{:else}
					<button class="nav" on:click={showQueue}>Show Queue</button>
				{/if}
			</div>
			<div class="list">
				{#if menu == "queue"}
					{#each $musicList as song,i}
						<!-- svelte-ignore a11y-click-events-have-key-events -->
						<div class="card {i==currentSong ? "active":""}" on:click={() => setPlay(i)}>
							<div class="card-img">
								<img src={"./uploads/img/" + song.image} alt={song.title}>
							</div>
							<div class="card-content">
								<h3>{song.title}</h3>
								<p>{song.artist}</p>
							</div>
						</div>
					{/each}
				{:else}
					<div class="lyrics">
						{@html $musicList[currentSong].lyrics}
					</div>
					<p class="genius">Lyrics by Genius</p>
				{/if}	
			</div>
		</div>
	</div>
</main>
