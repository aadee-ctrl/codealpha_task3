<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Music Player</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: #1a1a1a;
            margin: 0;
            font-family: Arial, sans-serif;
        }

        .music-player {
            background: #2a2a2a;
            padding: 2rem;
            border-radius: 15px;
            box-shadow: 0 0 20px rgba(0,0,0,0.3);
            width: 350px;
            color: white;
        }

        .song-info {
            text-align: center;
            margin-bottom: 1.5rem;
        }

        .song-title {
            font-size: 1.5rem;
            margin-bottom: 0.5rem;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .artist {
            color: #888;
            font-size: 1rem;
        }

        .progress-container {
            background: #404040;
            height: 5px;
            border-radius: 5px;
            margin: 1.5rem 0;
            cursor: pointer;
        }

        .progress {
            background: #1db954;
            height: 100%;
            width: 0%;
            border-radius: 5px;
            transition: width 0.1s linear;
        }

        .controls {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 1.5rem;
        }

        .control-btn {
            background: none;
            border: none;
            color: white;
            font-size: 1.5rem;
            cursor: pointer;
            padding: 0.5rem;
            transition: color 0.2s;
        }

        .control-btn:hover {
            color: #1db954;
        }

        .play-btn {
            font-size: 2rem;
        }

        .time-display {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
            color: #888;
            margin-top: 0.5rem;
        }

        .volume-container {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            margin-top: 1rem;
        }

        .volume-slider {
            width: 100px;
            height: 5px;
            -webkit-appearance: none;
            background: #404040;
            border-radius: 5px;
        }

        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 15px;
            height: 15px;
            background: #1db954;
            border-radius: 50%;
            cursor: pointer;
        }

        @keyframes marquee {
            0% { transform: translateX(100%); }
            100% { transform: translateX(-100%); }
        }

        .marquee {
            animation: marquee 10s linear infinite;
        }
    </style>
</head>
<body>
    <div class="music-player">
        <div class="song-info">
            <div class="song-title" id="song-title">Song Title</div>
            <div class="artist" id="artist">Artist</div>
        </div>
        
        <div class="progress-container" id="progress-container">
            <div class="progress" id="progress"></div>
        </div>
        
        <div class="time-display">
            <span id="current-time">0:00</span>
            <span id="duration">0:00</span>
        </div>

        <div class="controls">
            <button class="control-btn" id="prev-btn">⏮</button>
            <button class="control-btn play-btn" id="play-btn">▶</button>
            <button class="control-btn" id="next-btn">⏭</button>
        </div>

        <div class="volume-container">
            <span class="control-btn">🔊</span>
            <input type="range" class="volume-slider" id="volume" min="0" max="1" step="0.1" value="1">
        </div>
    </div>

    <script>
        const songs = [
            {
                title: "Summer Vibes",
                artist: "Chill Artist",
                src: "path/to/summer-vibes.mp3"
            },
            {
                title: "Night Drive",
                artist: "Synthwave Producer",
                src: "path/to/night-drive.mp3"
            },
            {
                title: "Morning Jazz",
                artist: "Coffee Shop Band",
                src: "path/to/morning-jazz.mp3"
            }
        ];

        const audio = new Audio();
        let currentSongIndex = 0;
        let isPlaying = false;

        const playBtn = document.getElementById('play-btn');
        const prevBtn = document.getElementById('prev-btn');
        const nextBtn = document.getElementById('next-btn');
        const progress = document.getElementById('progress');
        const progressContainer = document.getElementById('progress-container');
        const volumeSlider = document.getElementById('volume');
        const songTitle = document.getElementById('song-title');
        const artist = document.getElementById('artist');
        const currentTimeDisplay = document.getElementById('current-time');
        const durationDisplay = document.getElementById('duration');

        function loadSong(songIndex) {
            const song = songs[songIndex];
            audio.src = song.src;
            songTitle.textContent = song.title;
            artist.textContent = song.artist;
        }

        function playPause() {
            if (isPlaying) {
                audio.pause();
                playBtn.textContent = '▶';
            } else {
                audio.play();
                playBtn.textContent = '⏸';
            }
            isPlaying = !isPlaying;
        }

        function updateProgress(e) {
            const { duration, currentTime } = e.srcElement;
            const progressPercent = (currentTime / duration) * 100;
            progress.style.width = ${progressPercent}%;
            
            currentTimeDisplay.textContent = formatTime(currentTime);
            durationDisplay.textContent = formatTime(duration);
        }

        function setProgress(e) {
            const width = progressContainer.clientWidth;
            const clickX = e.offsetX;
            const duration = audio.duration;
            audio.currentTime = (clickX / width) * duration;
        }

        function formatTime(seconds) {
            const minutes = Math.floor(seconds / 60);
            seconds = Math.floor(seconds % 60);
            return ${minutes}:${seconds.toString().padStart(2, '0')};
        }

        function changeVolume() {
            audio.volume = volumeSlider.value;
        }

        function prevSong() {
            currentSongIndex = (currentSongIndex - 1 + songs.length) % songs.length;
            loadSong(currentSongIndex);
            if (isPlaying) audio.play();
        }

        function nextSong() {
            currentSongIndex = (currentSongIndex + 1) % songs.length;
            loadSong(currentSongIndex);
            if (isPlaying) audio.play();
        }

        playBtn.addEventListener('click', playPause);
        prevBtn.addEventListener('click', prevSong);
        nextBtn.addEventListener('click', nextSong);
        audio.addEventListener('timeupdate', updateProgress);
        progressContainer.addEventListener('click', setProgress);
        volumeSlider.addEventListener('input', changeVolume);
        audio.addEventListener('ended', nextSong);

        loadSong(currentSongIndex);
    </script>
</body>
</html>
