<script lang="ts">
    import Button from "$lib/Button.svelte";
    import { afterUpdate,beforeUpdate } from "svelte";
    import { Template } from "canvas-editor";
    import type { configuration } from "canvas-editor";

    import Input from "$lib/Input.svelte";

    let nom = "Rachel Kéké (50ans)"
    let circo = "7e circonscription du Val-de-Marne"
    let credit = ""
    let partie:string = ""

    let att1_titre:string = "Députée Sortante"
    let att1_desc:string= "Lutte pour la"
    let att2_titre:string= "Proposition"
    let att2_desc:string= "PORTER"
    let att3_titre:string= "Info bonus"
    let att3_desc:string= "22 mois"

    let loi :string="base"

    let files1: File[]

    let canvas : HTMLCanvasElement|undefined = undefined

    let template:Template
    let config:configuration

    let f1 = (new FontFace('Impact', 'url(./fonts/impact.woff)')).load()
    
    let loaded = new Promise( (resolve, reject) => Promise.all([f1]).then((r) => { 
        for (let f of r){
            document.fonts.add(f);
        }
        resolve(undefined)
    }))

    afterUpdate(async () =>{
        config = {
            backgroundURL:`./cards/POKEDEPUTE${partie}.PNG`,
            height:1039,
            width:744,
            canvas:canvas!
        }

        template = new Template(config)
    })

    beforeUpdate(async () =>{
        if (!template) return
        await loaded
        template.clear()

        if (files1) {
            // load une image 
            let image = await template.loadImageFile(files1[0])
            template.drawResizeCropImage(image, 70,123,600,393)
        }

        await template.drawBackground()

        template.drawTexte(nom,71,68,'Impact',33,"1","#aa2c4a",0,"left")
        template.drawTexte(circo,71,96,'Impact',20.7,"1","#aa2c4a",0,"left")
        template.drawTexte(credit,80,541,'Impact',20.7,"1","#a48199",0,"left")

        template.drawTexte(att1_titre,70,585,'Impact',25,"1","#f2ac9a",0,"left")
        template.drawTexte(att1_desc,70,619,'Impact',20.7,"1","#ded6c4",0,"left")

        let s = Math.min(330/template.wordSize(att2_desc,1),1)

        template.drawTexte(att2_titre,70,702,'Impact',25,"1","#f2ac9a",0,"left")
        template.drawTexte(att2_desc,71,744,'Impact',33*s,"1","#ded6c4",0,"left")

        template.drawTexte(att3_titre,70,804,'Impact',25,"1","#f2ac9a",0,"left")
        template.drawTexte(att3_desc,70,833,'Impact',25,"1","#ded6c4",0,"left")

        let image = await template.loadImageUrl(`./lois/${loi}.png`)
        template.drawResizeCropImage(image, 0,0,744,1039)
    })

</script>



<div class="relative grid gap-4 grid-cols-2 p-8 h-screen w-screen" spellcheck="false">

    <canvas class="h-full max-h-screen place-self-center border border-2" bind:this={canvas}></canvas>

    <div class="flex gap-4 flex-col overflow-y-auto overflow-x-visible">

        <Input placeholder="Nom" bind:value={nom}/>
        <Input placeholder="Circo" bind:value={circo}/>
        <Input placeholder="Crédits" bind:value={credit}/>
        <Input placeholder="Partie" type="select" bind:value={partie}>
            <option value="">Vide</option>
            <option value="_EELV">EELV</option>
            <option value="_LFI">LFI</option>
            <option value="_NPA">NPA</option>
            <option value="_PCF">PCF</option>
            <option value="_PS">PS</option>
        </Input>
        <Input placeholder="Illustration" type="file" bind:files={files1}/>
        
        

        <fieldset class="border p-4 flex gap-4 flex-col">
            <legend>Attaque 1</legend>

            <Input placeholder="Titre" bind:value={att1_titre}/>
            <Input type="textarea" placeholder="Description" bind:value={att1_desc}/>
        </fieldset>
        
        <fieldset class="border p-4 flex gap-4 flex-col">
            <legend>Attaque 2</legend>

            <Input placeholder="Titre" bind:value={att2_titre}/>
            <Input type="textarea" placeholder="Description" bind:value={att2_desc}/>

            <Input placeholder="Encadré" type="select" bind:value={loi}>
                <option value="base">Base</option>
                <option value="15jours">15 premiers jours</option>
                <option value="100jours">100 premiers jours</option>
                <option value="legis">Au cours de la législature</option>
            </Input>
        </fieldset>

        <fieldset class="border p-4 flex gap-4 flex-col">
            <legend>Attaque 3</legend>

            <Input placeholder="Titre" bind:value={att3_titre}/>
            <Input type="textarea" placeholder="Description" bind:value={att3_desc}/>
        </fieldset>

        <Button on:click={() => template.download()}>Télécharger</Button>


        
    </div>
</div>
