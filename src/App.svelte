<script>
    import { fade } from 'svelte/transition';
    import { cat, Problem } from './cat.svelte';
    import Crane from './Crane.svelte';
    import About from './About.svelte';

    let splash = {
        done: false,
        moment: 0,
        show: () => {
            splash.moment = 0;
            splash.done = false;
            if (!splash.done) setInterval(() => {
                if (splash.moment >= 100) splash.done = true;
                else if (splash.moment >= 90) splash.moment += 0.125;
                else if (splash.moment >= 50) splash.moment += 0.25;
                else if (splash.moment >= 25) splash.moment += 0.50;
                else splash.moment++;
            }, 10);
        }
    };

    let menu = {
        active: false,
        button: cat.logo.worktime,
        selected: 0,
        main: () => {
            menu.button = cat.logo.pinch;
            cat.log("🐱👈", "App.svelte");
            setTimeout(() => {
                menu.button = cat.logo.worktime;
            }, 200);
            menu.active = !menu.active;
        },
        option: [
            {
                name: "Reiniciar",
                action: () => {
                    cat.log("Reiniciando ...");
                    problem = new Problem;
                    menu.selected = 0;
                    menu.active = false;
                    splash.show();
                }
            },
            {
                name: "Grúa",
                action: () => {
                    menu.selected = 0;
                    menu.active = false;
                }
            },
            {
                name: "Acerca de",
                action: () => {
                    menu.selected = 1;
                    menu.active = false;
                }
            }
        ]
    };

    let problem = new Problem;

    splash.show();
</script>

<div class="flex w-screen h-screen bg-amber-600 overflow-x-hidden overscroll-none overflow-y-auto font-['Helvetica'] pt-24">
    {#if splash.done}
        <nav class="flex w-screen h-24 bg-amber-900 shadow-lg absolute top-0 items-center gap-2 px-4 z-50">
            <!-- svelte-ignore a11y-missing-attribute -->
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <img src={menu.button} class="absolute h-16 cursor-pointer" on:click={menu.main}/>
            <p class="w-full text-center font-['Oswald'] text-4xl text-white">
                {cat.title}
            </p>
        </nav>
        {#if menu.active}
            <div class="flex w-screen h-screen backdrop-blur-xl bg-black/25 absolute bottom-0 place-content-center items-center gap-8 px-4 z-49 flex-col text-2xl">
                {#each menu.option as option}
                <button class="w-full text-white bg-amber-900 p-2 rounded-lg" on:click={option.action}>
                    {option.name}
                </button>
                {/each}
            </div>
        {/if}
        {#if menu.selected == 0}
            <Crane bind:problem bind:menu bind:splash/>
        {:else if menu.selected == 1}
            <About/>
        {/if}
    {:else}
        <div
            class="flex
                    w-screen
                    h-screen
                    absolute
                    top-0
                    place-content-center
                    items-center
                    flex-col
                    bg-black"
            out:fade
        >
            <div class="flex place-content-center items-center w-full h-1/2 overflow-hidden">
                <!-- svelte-ignore a11y-missing-attribute -->
                {#if splash.moment <= 50}
                    <img class="absolute h-1/2 place-self-center" out:fade src={cat.logo.zzz}/>
                {:else}
                    <img class="absolute h-1/2 place-self-center" in:fade src={cat.logo.worktime}/>
                {/if}
            </div>
            <div class="bg-white rounded-xl h-2 place-self-center w-2/3">
                <div class="bg-amber-600 h-full rounded-xl" style="width: {splash.moment}%"></div>
            </div>
            <p class="font-['Oswald'] text-white text-center w-2/3 text-xl">{Math.floor(splash.moment)}%</p>
        </div>
    {/if}
</div>