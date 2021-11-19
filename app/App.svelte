<page>
    <actionBar title="Svelte Native App" />
    <stackLayout>
        <label textWrap="{true}">

            <formattedString>
                <span text="Take break every 45 minutes when you are working" />
                <!-- <span text="Walk/run/jump/ponder/observe/thanks to allah/sorry allah" />
                <span text="After 5 minutes break . go back to work" /> -->
                
            </formattedString>
        </label>
        <label text="allah akbar">
        </label>
        <label text="break activity: walk in the same place 5 minutes ">
        </label>
        <label text="other break actitivies: say thanks/sorry/observe"></label>
        <label text="Count Down : {countdown}"></label>
        <label text="Time : {timeMessage}"/>
        <label text="Status: {workBreakMessage}"/>
        <button text="Pause" on:tap={pauseAudio}/>
        <button text="play" on:tap={playAudio}/>
        <button text="Resume" on:tap={resumeAudio}/>
    </stackLayout>
</page>

<script lang="typescript">
    import { onMount } from "svelte";
    import {
        AudioPlayerOptions,
        AudioRecorderOptions,
        TNSPlayer,
        TNSRecorder
    } from 'nativescript-audio';

    import {knownFolders } from '@nativescript/core';

    let  player =new TNSPlayer();
    const audioFolder = knownFolders.currentApp().getFolder('audio');
    // console.log('audio folder: ' + audioFolder);
    console.log("audio folder:" + JSON.stringify(audioFolder));
    const recordedFile = audioFolder.getFile('angel.mp3');


    function playAudio(){
        player.playFromUrl(playerOptions).catch(err => {
        console.log('error playFromFile');
        //   this.isPlaying = false;
        });
    }
    function pauseAudio() {
       if(player.isAudioPlaying())
        player.pause();
    }
    function resumeAudio(){
        player.resume();
    }
    const playerOptions: AudioPlayerOptions = {
    //   audioFile: '~/audio/angel.mp3',
        audioFile: 'https://sveltejs.github.io/assets/music/strauss.mp3',
    // let audioTrack = 'https://sveltejs.github.io/assets/music/strauss.mp3';
      loop: false,
      completeCallback: async () => {
        alert('Audio file complete.');
        // if (!playerOptions.loop) {
        //   await player.dispose();
        //   console.log('player disposed');
        // }
      },
      errorCallback: errorObject => {
        console.log("error while playing audio " + JSON.stringify(errorObject));
      },

      infoCallback: infoObject => {
        console.log("info callback audio :" + JSON.stringify(infoObject));
        // Dialogs.alert('Info callback');
      }
    };

    // let audioTrack = 'https://sveltejs.github.io/assets/music/strauss.mp3';
       
    // let timer: string =""; // time will com here
    // let countdown = 45 * 60; // 45 minutes
    let countdown = 5; // 5 minutes
    let workCountDown = 2 * 60;
    let breakCountDown = 1 * 60;
    let workBreak =false; // true work, false break
    let timer;
    $: timeMessage = Math.floor(countdown/60) + "m " + (countdown%60) + " s";
    $: workBreakMessage = workBreak? "Working time": "Break time";
    $: {
        if (countdown === 0) {
            if (timer) {
                clearInterval(timer);
                timer = null;
                if(workBreak){
                    workBreak  = !workBreak ; // start break timer
                    countdown = breakCountDown;
                    timer = setInterval(() => {
                        countdown -= 1;
                    }, 1000);
                } else {
                    // start work timer
                    workBreak  = !workBreak ; // start work timer
                    countdown = workCountDown;
                    timer = setInterval(() => {
                        countdown -= 1;
                    }, 1000);
                }
                playAudio();
            }
        }
    }
    onMount(() => {
        timer = setInterval(() => {
        countdown -= 1;
        }, 1000);
    });
</script>

<style>
</style>
