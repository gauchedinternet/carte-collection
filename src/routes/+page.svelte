<script lang="ts">
    import Button from "$lib/Button.svelte";
    import { afterUpdate, onMount } from "svelte";

    import { Template, CE_Text, CE_Picture, CE_Vec2, CE_FormattedText } from "canvas-editor";

    import Input from "$lib/Input.svelte";

    let template:Template = new Template(new CE_Vec2(744,1039))

    let files1: File[]
    let image1 = new CE_Picture()
    image1.position = new CE_Vec2(70,122)
    image1.resize(new CE_Vec2(600,394))
    template.add(image1)

    let background = new CE_Picture()
    template.add(background)

    let nom:CE_Text = new CE_Text('Impact',33,"400","#aa2c4a",0,"left")
    nom.data = "Rachel Kéké (50ans)"
    nom.position = new CE_Vec2(71,68)
    template.add(nom)

    let circo:CE_Text = new CE_Text('Impact',20.7,"400","#aa2c4a",0,"left")
    circo.data = "7e circonscription du Val-de-Marne"
    circo.position = new CE_Vec2(71,96)
    template.add(circo)

    let credit:CE_Text = new CE_Text('Impact',20.7,"400","#a48199",0,"left")
    credit.position = new CE_Vec2(80,541)
    template.add(credit)
    
    let att1_titre:CE_Text = new CE_Text('Impact',33,"400","#aa2c4a",0,"left")
    att1_titre.position = new CE_Vec2(70,585)
    template.add(att1_titre)

    let f1 = (input:string) => {
        return {
            fontSize:20.7,
            fontWeight:400,
            color:"#ded6c4",
            specialColor:"#ded6c4",
            fontFamily:'Impact',
            textAlign:"left",
            output:input,
            letterSpacing:0
        }
    }
    let att1_desc:CE_Text = new CE_FormattedText(f1,670)
    att1_desc.position = new CE_Vec2(70,619)
    template.add(att1_desc)

    let att2_titre:CE_Text = new CE_Text('Impact',25,"400","#f2ac9a",0,"left")
    att2_titre.position = new CE_Vec2(70,702)
    template.add(att2_titre)

    let att2_desc:CE_Text = new CE_Text('Impact',33,"400","#ded6c4",0,"left")
    att2_desc.position = new CE_Vec2(71,744)
    att2_desc.width = 480
    template.add(att2_desc)

    let att3_titre:CE_Text = new CE_Text('Impact',25,"400","#f2ac9a",0,"left")
    att3_titre.position = new CE_Vec2(70,804)
    template.add(att3_titre)

    let att3_desc:CE_Text = new CE_FormattedText(f1,670)
    att3_desc.position = new CE_Vec2(70,833)
    template.add(att3_desc)

    let partie:string = ""
    let loi_image = new CE_Picture()
    let loi :string="base"
    template.add(loi_image)

    onMount(() => {        
        loop()
    })

    afterUpdate(async () =>{
        await background.loadFromUrl(`./cards/POKEDEPUTE${partie}.PNG`)

        document.fonts.add(await (new FontFace('Impact', 'url(./fonts/impact.woff)')).load())

        if (files1){
            await image1.loadFromFile(files1[0])
        }

        await loi_image.loadFromUrl(`./lois/${loi}.png`)
    })

    function loop(){
        if (template.destroyed) {return}
        template.draw()
        requestAnimationFrame(loop);
    }

</script>



<div class="relative grid gap-4 grid-cols-2 p-8 h-screen w-screen" spellcheck="false">

    <canvas class="h-full max-h-screen place-self-center border border-2" bind:this={template.canvas}></canvas>

    <div class="flex gap-4 flex-col overflow-y-auto overflow-x-visible">

        <Input placeholder="Nom" bind:value={nom.data}/>
        <Input placeholder="Circo" bind:value={circo.data}/>
        <Input placeholder="Crédits" bind:value={credit.data}/>
        <Input placeholder="Partie" type="select" bind:value={partie}>
            <option value="">Vide</option>
            <option value="_EELV">EELV</option>
            <option value="_LFI">LFI</option>
            <option value="_NPA">NPA</option>
            <option value="_PCF">PCF</option>
            <option value="_PS">PS</option>
        </Input>
        <Input placeholder="Illustration" type="file" bind:files={files1}/>

        <Input placeholder="Alignement Horizontal" type="range" step={0.1} min={-1} max={1} bind:value={image1.anchor.x}/>
        <Input placeholder="Alignement Vertical" type="range" step={0.1} min={-1} max={1} bind:value={image1.anchor.y}/>

        <fieldset class="border p-4 flex gap-4 flex-col">
            <legend>Attaque 1</legend>

            <Input placeholder="Titre" bind:value={att1_titre.data}/>
            <Input type="textarea" placeholder="Description" bind:value={att1_desc.data}/>
        </fieldset>
        
        <fieldset class="border p-4 flex gap-4 flex-col">
            <legend>Attaque 2</legend>

            <Input placeholder="Titre" bind:value={att2_titre.data}/>
            <Input type="textarea" placeholder="Description" bind:value={att2_desc.data}/>

            <Input placeholder="Encadré" type="select" bind:value={loi}>
                <option value="base">Base</option>
                <option value="15jours">15 premiers jours</option>
                <option value="100jours">100 premiers jours</option>
                <option value="legis">Au cours de la législature</option>
            </Input>
        </fieldset>

        <fieldset class="border p-4 flex gap-4 flex-col">
            <legend>Attaque 3</legend>

            <Input placeholder="Titre" bind:value={att3_titre.data}/>
            <Input type="textarea" placeholder="Description" bind:value={att3_desc.data}/>
        </fieldset>

        <Button on:click={() => template.download()}>Télécharger</Button>


        
    </div>
</div>
