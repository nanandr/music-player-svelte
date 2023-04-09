<script>
	import {onMount} from "svelte";
	import {musicList} from "./musiclist.js";

	let currentSong = 0;
	let playerState = "play";
	let menu = "queue";
	let audioElement;
	let mainElement;

	function prev(){
		if(currentSong > 0){
			currentSong--;
			playerState = "play"
		}
	}
	function playpause(){
		if(playerState == "play"){
			playerState = "pause";
			audioElement.pause();
		}
		else{
			playerState = "play"
			audioElement.play();
		}
	}
	function next(){
		if(currentSong < $musicList.length-1){
			currentSong++;
			playerState = "play"
		}
	}
	function setPlay(i){
		currentSong = i;
		playerState = "play";
	}
	function showLyrics(){
		menu = "lyrics";
	}
	function showQueue(){
		menu = "queue";
	}
</script>

<main bind:this={mainElement}>
	<nav>
		<h1>Sveltify</h1>
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
							<i class="fa-backward fa-solid icon"></i>
						</button>
						<button on:click={playpause}>
							{#if playerState == "play"}
								<i class="fa-pause fa-solid pause icon"></i>
							{:else}
								<i class="fa-play fa-solid play icon"></i>
							{/if}
						</button>
						<button on:click={next}>
							<i class="fa-forward fa-solid icon"></i>
						</button>
					</div>
				</div>				
				<audio
					class="audio"
					src={"./uploads/audio/" + $musicList[currentSong].audio}
					bind:this={audioElement}
					autoplay="false"
					controls
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
