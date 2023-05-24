<script>
    import { cat } from './cat.svelte';
    import { fade } from 'svelte/transition';
    export let problem;
    export let menu;
    export let splash;
    let input = {
        header: "font-bold font-['Oswald'] w-full",
        self: "text-right w-10/12 bg-amber-200 rounded-lg px-2",
        div: "text-center gap-1 bg-amber-500 rounded-lg mb-2 flex flex-wrap p-2"
    };

    $: if (problem.mass < 0 || typeof problem.mass !== 'number') problem.mass = '';
    $: if (problem.P.length < 0 || typeof problem.P.length !== 'number') problem.P.length = '';
    $: if (problem.P.angle < 0 || typeof problem.P.angle !== 'number') problem.P.angle = '';
    $: if (problem.cable.counterMass < 0 || typeof problem.cable.counterMass !== 'number') problem.cable.counterMass = '';
    $: if (problem.cable.angle.magnitude < 0 || typeof problem.cable.angle.magnitude !== 'number') problem.cable.angle.magnitude = '';
    
    $: problem.calcWeight ();
    $: problem.calcP ();
    $: problem.calcMomentum ();
    $: problem.calcTension ();
    $: problem.calcCTension ();
    $: problem.calcCounterW ();
</script>

<div class="flex flex-col justify-items-end px-2 pt-2 w-full h-full">
    <div class="flex flex-row justify-between items-center place-content-center flex-wrap">
        <div class="w-1/3 flex flex-col">
            <div class={input.div}>
                <p class={input.header}>Masa</p>
                <input class={input.self} type="number" bind:value={problem.mass} min=0>kg
            </div>
            <div class={input.div}>
                <p class={input.header}>Brazo</p>
                <input class={input.self} type="number" bind:value={problem.P.length} min=0>m
                <input class={input.self} type="number" bind:value={problem.P.angle} min=0>°
            </div>
            <div class={input.div}>
                <p class={input.header}>Cable</p>
                <input class={input.self} type="number" bind:value={problem.counterMass} min=0>kg
                <input class={input.self} type="number" bind:value={problem.cable.angle.magnitude} min=0>°
                <div class="flex w-full flex-col justify-items-end"><p class={input.header}>Respecto al brazo</p><input type="checkbox" class="w-8 h-8 mx-auto" bind:checked={problem.cable.angle.betweenP} min=0></div>
            </div>        
        </div>

        <!-- svelte-ignore a11y-missing-attribute -->
        <img src={cat.crane} class="w-[50vw] h-[45vw] mx-auto"/>
    </div>

    <div class="flex bg-amber-400 rounded-lg p-2 flex-col gap-2" class:hidden={menu.active || !splash.done} in:fade>
        {@html cat.formula("\\vec{W}=" + problem.mass + "kg\\times9.81\\frac{m}{s^2}=" + problem.Wvector.toFixed(2) + "N\\hat{j}")}
        {@html cat.formula("\\vec{P}=" + problem.P.length + "m\\cos{" + problem.P.angle + "}\\degree\\hat{i}+" + problem.P.length + "m\\sin{" + problem.P.angle + "}\\degree\\hat{j}=" + problem.P.x.toFixed(2) + "m\\hat{i}+" + problem.P.y.toFixed(2) + "m\\hat{j}")}
        {@html cat.formula("\\LARGE\\mu\\normalsize_A=\\begin{matrix}\\\\\\vec{P}\\\\\\vec{W}\\end{matrix}\\begin{vmatrix}\\hat{i}&\\hat{j}&\\hat{k}\\\\" + problem.P.x.toFixed(2) + "m&" + problem.P.y.toFixed(2) + "m&0\\\\0&" + problem.Wvector.toFixed(2) + "N&0\\end{vmatrix}=" + problem.Momentum.toFixed(2) + "N\\cdot m\\hat{k}")}
        {@html cat.formula("T_{necesaria}=\\frac{-\\large\\mu\\normalsize_A}{\\lvert\\vec{P}\\rvert\\sin{" + (problem.cable.angle.betweenP ? problem.cable.angle.magnitude:(180-((180-problem.P.angle) + problem.cable.angle.magnitude))) + "\\degree}}=\\frac{" + ((-1)*problem.Momentum).toFixed(2) + "N\\cdot m\\hat{k}}{" + problem.P.length + "m\\sin{" + (problem.cable.angle.betweenP ? problem.cable.angle.magnitude:(180-((180-problem.P.angle) + problem.cable.angle.magnitude))) + "\\degree}}=" + (problem.cable.tension.toFixed(4) + "N").replace('NaNN', '\\nexists').replace('InfinityN', '\\infty'))}
        Contrapeso necesario
        {@html cat.formula("m_{{cable}_{necesaria}}=\\frac{T_{necesaria}\\cdot\\sin{" + (!problem.cable.angle.betweenP ? problem.cable.angle.magnitude:(180-((180-problem.P.angle) + problem.cable.angle.magnitude))) + "\\degree}}{9.81\\frac{m}{s^2}}=" + (problem.counterW.toFixed(4) + "kg").replace('NaNkg', '\\nexists').replace('Infinitykg', '\\infty') + (problem.counterW <= 0 ? "\\therefore\\nexists" : ""))}
        Tensión real
        {@html cat.formula("T_{real}=\\frac{m_{cable}\\cdot9.81\\frac{m}{s^2}}{sin{" + (!problem.cable.angle.betweenP ? problem.cable.angle.magnitude:(180-((180-problem.P.angle) + problem.cable.angle.magnitude))) + "\\degree}}=" + (problem.cable.ctension.toFixed(4) + "N").replace('NaNN', '\\nexists').replace('InfinityN', '\\infty'))}
        Tensión a favor
        {@html cat.formula("T_{real}-T_{necesaria}=" + ((problem.cable.ctension - problem.cable.tension).toFixed(4) + "N").replace('NaNN', '\\nexists').replace('InfinityN', '\\infty'))}
    </div>


    <div class="mt-2 rounded-lg bg-amber-900 p-2">
        {#if (problem.cable.tension <= 0 || problem.counterW <= 0 || problem.P.angle <= 0 || problem.P.angle >= 90 || problem.cable.tension.toString() == 'NaN' || problem.cable.tension.toString() == "Infinity")}
            <p class="font-['Oswald'] text-center text-xl text-white">Verifique los datos.</p>
        {:else if (problem.cable.ctension - problem.cable.tension) >= (problem.cable.tension*0.10)}
            <p class="font-['Oswald'] text-center text-xl text-green-400">Puede operar la grúa adecuadamente.</p>
        {:else if (problem.cable.ctension - problem.cable.tension) >= 0}
            <p class="font-['Oswald'] text-center text-xl text-yellow-500">No debería operar la grúa en condiciones reales, ya que cualquier fuerza incidente en el eje Y hacia abajo puede volcar su equipo o romper el cable. Se recomienda cambiar el contrapeso para producir una tensión de {(problem.cable.tension+(problem.cable.tension*0.10)).toFixed(4)}N, que es 10% más de lo estrictamente necesario para un equilibrio estático.</p>
        {:else if (problem.cable.ctension - problem.cable.tension) < 0}
            <p class="font-['Oswald'] text-center text-xl text-red-500">¡ALTO! No intente operar la grúa. Su cable es demasiado débil. Se recomienda cambiar el contrapeso para producir una tensión de {(problem.cable.tension+(problem.cable.tension*0.10)).toFixed(4)}N, que es 10% más de lo estrictamente necesario para un equilibrio estático.</p>
        {/if}        
    </div>
    <br>
</div>