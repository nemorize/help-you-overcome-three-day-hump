<script lang="ts">
    import { goto } from '$app/navigation';
    import { page } from "$app/stores";

    const steps = [ 'intro', 'get-started', 'process', 'complete' ] as const;
    $: step = ($page.params.step ?? 'intro') as (typeof steps[number])|string;
    $: if (!steps.includes(step as any)) {
        goto('/');
    }

    let nickName: string = '';
    let mainGoal: string = '';
    let detailGoal: string = '';
    $: disabled = nickName.trim() === '' || mainGoal.trim() === '' || detailGoal.trim() === '';

    let processFinished: boolean = false;
    $: if (step === 'process') {
        setTimeout(() => {
            processFinished = true;
            setTimeout(() => {
                goto('/complete');
            }, 3000);
        }, Math.floor(Math.random() * 3000) + 5000);
    }

    let played: boolean = false;
    let playerEl: HTMLDivElement;
    let youtubePlayer: YT.Player|null = null;

    $: if (step === 'complete') {
        if (!document.querySelector('#youtube-iframe-api')) {
            // @ts-ignore
            window.onYouTubeIframeAPIReady = () => {
                youtubePlayer = new YT.Player(playerEl, {
                    width: 560,
                    height: 315,
                    videoId: 'dQw4w9WgXcQ'
                });
            };

            const scriptEl = document.createElement('script');
            scriptEl.id = 'youtube-iframe-api';
            scriptEl.src = 'https://www.youtube.com/iframe_api';
            document.body.appendChild(scriptEl);
        }
    }

    const onPlayClick = () => {
        if (played) {
            return;
        }
        played = true;
        youtubePlayer?.playVideo();
    };

    const onShareClick = () => {
        const url = `https://${location.host}/`;

        if (!navigator.share) {
            if (!navigator.clipboard) {
                return prompt(
                    'Your browser does not support sharing function. Please copy the URL below and paste to your wants.',
                    url
                );
            }

            return navigator.clipboard.writeText(
                'Help you overcome 3-day hump!\n' +
                'Tell us what you\'d like to accomplish, and we\'ll use AI to create a motivational 3:30 video for you.\n\n' +
                url
            ).then(_ => {
                alert('URL has been copied into clipboard! Paste it to your wants.');
            });
        }

        navigator.share({
            url,
            title: 'Help you overcome 3-day hump!',
            text: 'Tell us what you\'d like to accomplish, and we\'ll use AI to create a motivational 3:30 video for you.'
        });
    };
</script>

<main>
    {#if step !== 'process'}
        <header class:large={step === 'intro'}>
            {#if step === 'intro'}
                <h1>
                    Help You<br />
                    Overcome<br />
                    3-Day Hump!
                </h1>
                <p>
                    The year 2024 has arrived.
                    We're here to help you stay on track with your New Year's resolutions.<br />
                    Tell us what you'd like to accomplish, and we'll use AI to create a motivational 3:30 video for you.
                </p>
                <button type="button" on:click={_ => goto('/get-started')}>
                    Start motivating!
                </button>
            {:else if step === 'get-started'}
                <h1>Welcome!</h1>
                <p>
                    Here's to a successful new year!<br />
                    Let us know what you want to accomplish by filling out the form below!
                </p>
            {:else if step === 'complete'}
                <h1>Here you are!</h1>
                <p>
                    We're always rooting for you.<br />
                    Never give up on your resolutions this year!
                </p>
            {/if}
        </header>
    {/if}

    {#if step === 'intro'}
        <section>

        </section>
    {:else if step === 'get-started'}
        <section class="get-started">
            <label>
                <b>Nickname</b>
                <span>Let us know what you'd like us to call you.</span>
                <input type="text" placeholder="Nick P. Nestley" bind:value={nickName} />
            </label>
            <label>
                <b>Main goal</b>
                <span>In five words or less, tell us your top goals for this year. By limiting the word count, your goals become a little clearer.</span>
                <input type="text" placeholder="Stop smoking" bind:value={mainGoal}/>
            </label>
            <label>
                <b>Detail of your goals</b>
                <span>Please write down in detail what you want to accomplish. It's also helpful to include things that aren't your main goal.</span>
                <textarea placeholder="" bind:value={detailGoal}></textarea>
            </label>
            <button type="button" class="primary" disabled={disabled} on:click={_ => goto('/process')}>
                Generate!
            </button>
        </section>
    {:else if step === 'process'}
        <section class="process">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="12" x2="12" y1="2" y2="6" />
                <line x1="12" x2="12" y1="18" y2="22" />
                <line x1="4.93" x2="7.76" y1="4.93" y2="7.76" />
                <line x1="16.24" x2="19.07" y1="16.24" y2="19.07" />
                <line x1="2" x2="6" y1="12" y2="12" />
                <line x1="18" x2="22" y1="12" y2="12" />
                <line x1="4.93" x2="7.76" y1="19.07" y2="16.24" />
                <line x1="16.24" x2="19.07" y1="7.76" y2="4.93" />
            </svg>
            <h1>
                {processFinished ? 'Almost finished!' : 'We\'re working on a video.'}
            </h1>
            <p>
                We're creating a video based on the information you've given us, which will be uploaded to YouTube for viewing with a link.
            </p>
        </section>
    {:else if step === 'complete'}
        <section class="complete" class:hide-poster={played}>
            <div class="video">
                <div class="poster" role="button" tabindex="0" on:click={onPlayClick} on:keypress={onPlayClick}>
                    {#if youtubePlayer}
                        <span>
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="currentColor" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                <polygon points="5 3 19 12 5 21 5 3"/>
                            </svg>
                        </span>
                        <time>3:32</time>
                    {:else}
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <line x1="12" x2="12" y1="2" y2="6" />
                            <line x1="12" x2="12" y1="18" y2="22" />
                            <line x1="4.93" x2="7.76" y1="4.93" y2="7.76" />
                            <line x1="16.24" x2="19.07" y1="16.24" y2="19.07" />
                            <line x1="2" x2="6" y1="12" y2="12" />
                            <line x1="18" x2="22" y1="12" y2="12" />
                            <line x1="4.93" x2="7.76" y1="19.07" y2="16.24" />
                            <line x1="16.24" x2="19.07" y1="7.76" y2="4.93" />
                        </svg>
                    {/if}
                </div>
                <div class="player" bind:this={playerEl}></div>
            </div>
            {#if played}
                <div class="description">
                    <h1>Never gonna give you up!</h1>
                    <p>
                        Yes, it's just a meme, but it's a surprisingly meaningful prank.
                    </p>
                    <p>
                        We genuinely want you to work towards your goals: quit smoking, cut down on the alcohol,
                        and lose that fatty lump you can see when you hang your head, even if it's only 3 kilograms.
                    </p>
                    <p>
                        In the East, including Korea, 2024 is called the Year of the Blue Dragon.
                        Like the dragon, which symbolizes courage and challenge,
                        I hope you achieve your goals this year.
                    </p>
                    <p>
                        Yes, literally <b>never gonna give you up!</b>
                    </p>
                </div>
            {/if}

            <div class="description">
                {#if played}
                    <h1>Frank your friends!</h1>
                    <button type="button" on:click={onShareClick}>
                        Share the link!
                    </button>
                {:else}
                    <h1>Share your video!</h1>
                    <p>
                        First, check out the generated video.
                    </p>
                {/if}
            </div>
        </section>
    {/if}
</main>

<style lang="scss">
    main {
        display: flex;
        flex-direction: column;
        align-items: stretch;
        justify-content: space-between;

        width: 100%;
        height: 100%;
    }

    header {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        width: 100%;
        padding: 64px 20px;

        background-color: #FF90E8;
        border-bottom: 2px solid #010305;

        h1 {
            margin: 0;
            padding: 0;

            color: #010305;
            font-size: 40px;
            font-weight: 400;
            line-height: 1.125;
            text-align: center;
        }

        p {
            margin: 24px 0 0;
            padding: 0;

            color: #010305;
            font-size: 16px;
            line-height: 1.325;
            text-align: center;
        }

        button {
            margin: 32px 0 0;
        }

        &.large {
            height: calc(100vh - 8px - 4px);
            border-bottom: 0 none;
        }
    }

    section.get-started {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        justify-content: flex-start;

        width: 100%;
        padding: 20px;

        label {
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            justify-content: flex-start;

            width: 100%;
            margin: 32px 0 0;

            &:first-child {
                margin: 0;
            }

            b {
                padding-left: 2px;

                color: #010305;
                font-size: 16px;
                font-weight: 600;
            }

            span {
                margin: 4px 0 0;
                padding: 0 0 0 2px;

                color: #010305;
                font-size: 14px;
                font-weight: 400;
                line-height: 1.325;
            }

            input, textarea {
                width: 100%;
                margin: 12px 0 0;
                padding: 8px 12px;

                outline: none;
                border: 2px solid #010305;
                border-radius: 4px;

                color: #010305;
                font-size: 14px;
                line-height: 1;

                transition: transform 0.325s, box-shadow 0.325s;

                &:focus {
                    transform: translate(-3px, -3px);
                    box-shadow: 3px 3px 0 #010305;
                }
            }

            textarea {
                height: 180px;
                resize: none;
                overflow-y: auto;

                line-height: 1.325;
            }
        }

        button {
            width: 100%;
            margin-top: 20px;
        }
    }

    section.process {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        width: 100%;
        height: calc(100vh - 8px - 4px);
        padding: 0 20px;

        svg {
            width: 32px;
            height: 32px;

            animation: rotate 1.75s infinite linear;
        }

        h1 {
            margin: 32px 0 0;
            padding: 0;

            color: #010305;
            font-size: 20px;
            line-height: 1;
        }

        p {
            margin: 12px 0 0;
            padding: 0;

            color: #010305;
            font-size: 14px;
            line-height: 1.325;
            text-align: center;
        }
    }

    section.complete {
        padding: 8px;

        .video {
            position: relative;

            width: 100%;
            aspect-ratio: 560/315;
            overflow: hidden;

            border-radius: 4px;

            .poster {
                position: absolute;
                top: 0;
                left: 0;
                z-index: 10;

                display: flex;
                align-items: center;
                justify-content: center;

                width: 100%;
                height: 100%;

                background-color: #FFC900;
                border: 2px solid #010305;

                span {
                    display: flex;
                    align-items: center;
                    justify-content: center;

                    width: 56px;
                    height: 40px;

                    background-color: #010305;
                    border-radius: 4px;

                    svg {
                        width: 20px;
                        height: 20px;

                        color: #FFF;
                    }
                }

                time {
                    position: absolute;
                    bottom: 8px;
                    right: 10px;

                    color: #010305;
                    font-size: 12px;
                    line-height: 1;
                }

                & > svg {
                    animation: rotate 1.75s infinite linear;
                }
            }

            :global(iframe) {
                position: relative;
                z-index: 0;

                width: 100%;
                height: 100%;

                pointer-events: none;
            }
        }

        &.hide-poster {
            .poster {
                display: none !important;
            }

            :global(iframe) {
                pointer-events: all !important;
            }
        }

        .description {
            width: calc(100% + 16px);
            margin: 8px 0 0 -8px;
            padding: 20px;

            border-top: 2px solid #010305;

            h1 {
                margin: 0 0 8px;
                padding: 0;
            }

            p {
                margin: 12px 0 0;
                padding: 0;

                color: #010305;
                font-size: 15px;
                line-height: 1.5;

                &:last-child {
                    margin-bottom: -8px;
                }
            }

            button {
                width: 100%;
                margin: 12px 0 -6px;
            }
        }
    }

    button {
        padding: 12px 24px;

        appearance: none;
        -webkit-appearance: none;
        cursor: pointer;

        background-color: #FFF;
        border: 2px solid #010305;
        border-radius: 4px;
        outline: none;

        color: #010305;
        font-size: 16px;
        text-decoration: none;
        line-height: 1;

        transition: background-color 0.325s, border-color 0.325s, color 0.325s, transform 0.325s, box-shadow 0.325s;

        &:hover:not([disabled]) {
            background-color: #FFF;
            color: #010305;

            transform: translate(-3px, -3px);
            box-shadow: 3px 3px 0 #010305;
        }

        &.primary {
            background-color: #010305;
            color: #FFF;

            &:hover:not([disabled]) {
                background-color: #0088FF;
                border-color: #0088FF;
            }
        }

        &[disabled] {
            cursor: not-allowed;
            background-color: #EBECED;
            border-color: #E0E1E2;
            color: #C9CACB;
        }
    }

    @keyframes rotate {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
</style>