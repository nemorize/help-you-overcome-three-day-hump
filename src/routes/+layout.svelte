<script lang="ts">
    const title = 'Help You Overcome Three-Day Hump!';
    const description = 'Tell us what you\'d like to accomplish, and we\'ll use AI to create a motivational 3:30 video for you.';
</script>

<svelte:head>
    <title>{title}</title>
    
    <meta property="og:type" content="website" />
    <meta property="og:locale" content="en_US" />

    <meta property="og:url" content="https://${location.host}/" />
    <meta property="og:title" content="{title}" />
    <meta property="og:description" content="{description}" />

    <meta name="twitter:card" content="summary" />
    <meta name="twitter:title" content="{title}" />
    <meta name="twitter:description" content="{description}" />
</svelte:head>

<div id="app">
    <slot />
</div>

<style lang="scss">
    @import url('https://fonts.googleapis.com/css2?family=DM+Sans:opsz,wght@9..40,200;9..40,400;9..40,600&display=swap');

    :global(*) {
        box-sizing: border-box;
        font-family: 'DM Sans', sans-serif;

        &:before, &:after {
            box-sizing: inherit;
            font-family: inherit;
        }
    }

    :global(html), :global(body) {
        width: 100%;
        height: 100%;
        margin: 0;
        overflow-x: hidden;
    }

    :global(body) {
        padding: 4px;
        background-color: #EBECED;

        &:after {
            content: '';
            display: block;
            width: 100%;
            height: 1px;
            margin-top: -1px;
        }
    }

    #app {
        width: 100%;
        max-width: 440px;
        min-height: 100%;
        margin: 0 auto;
        padding: 0;

        background-color: #FFF;
        border: 2px solid #010305;
    }
</style>