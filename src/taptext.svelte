<script>
import DictPopup from "./dictpopup.svelte"
import Foliolist from "./foliolist.svelte"
import Audio from "./audio.svelte"
import About from "./about.svelte"
import SourceText from './sourcetext.svelte'
import Translations from "./translations.svelte"
import Variorum from "./variorum.svelte"
import Favorite from "./favorite.svelte"
import Toc from "./toc.svelte"
import {activePtk,advancemode,activefolioid, hasVariorum,hasSanskrit,bookByFolio} from './store.js'
import { usePtk} from "ptk";
export let tofind='';
export let address='';
export let closePopup;
let thetab='toc';
let ptk ,entries=[];

const onDict=async (t)=>{
    const tap_at=t.indexOf('^');
    entries=ptk.columns.entries.keys.findMatches( t.replace('^','')).map(it=>[Math.abs(it[0]-tap_at-1),it[1],it[2]]);
    entries.sort((a,b)=> a[0]-b[0]);// 越接近點擊處的優先
    if (entries.length) {
        showdict=true;
        showmainmenu=false;
        thetab='dict'
    } else if (~address.indexOf('ck')) {
        thetab=$advancemode=='on'?'translations':'favorite';
    }
}
$: ptk=usePtk($activePtk);
$: onDict(tofind)
</script>
<div class="popup">
    <div class="tabs">    

        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span class='clickable' class:selected={thetab=="about"} on:click={()=>thetab="about"}>⚙️</span>

        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span class='clickable' class:selected={thetab=="list"} on:click={()=>thetab="list"}>📓</span>
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span class='clickable' class:selected={thetab=="toc"} on:click={()=>thetab="toc"}>🧭</span>
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span class='clickable' class:selected={thetab=="favorite"} on:click={()=>thetab="favorite"}>❤️</span>

        
        {#if ~address.indexOf('ck') && $advancemode=='on'}
        {#if hasSanskrit(bookByFolio($activefolioid))}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span class='clickable' class:selected={thetab=="sourcetext"} on:click={()=>thetab="sourcetext"}>📜</span>
        {/if}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span class='clickable' class:selected={thetab=="translations"} on:click={()=>thetab="translations"}>🔀</span>
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        {#if hasVariorum(bookByFolio($activefolioid))}
        <span class='clickable' class:selected={thetab=="variorum"} on:click={()=>thetab="variorum"}>📚</span>
        {/if}
        {/if}
        {#if $advancemode!=='on'}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <span class='clickable' class:selected={thetab=="audio"} on:click={()=>thetab="audio"}>🎵</span>
        {/if}   
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        {#if entries.length} <span class='clickable' class:selected={thetab=="dict"} on:click={()=>thetab="dict"}>🔎</span>{/if}

        

    </div>
    
      <div class="tab-content" class:visible={thetab=='list'}><Foliolist {ptk} {closePopup}/></div>
      
      <div class="tab-content" class:visible={thetab=='toc'}><Toc {address} {closePopup} {ptk} /></div>
      <div class="tab-content" class:visible={thetab=='favorite'}><Favorite {address} {closePopup} {ptk} /></div>
      <div class="tab-content" class:visible={thetab=='sourcetext'}><SourceText {closePopup} bind:address {ptk}/></div>
      <div class="tab-content" class:visible={thetab=='translations'}><Translations {closePopup} bind:address {ptk}/></div>
      
      <div class="tab-content" class:visible={thetab=='variorum'}><Variorum {closePopup} bind:address {ptk}/></div>
      


      {#if entries.length}
      <div class="tab-content" class:visible={thetab=='dict'}><DictPopup {entries} {ptk}/></div>
      {/if}
      <div class="tab-content" class:visible={thetab=='audio'}><Audio {ptk}/></div>
      <div class="tab-content" class:visible={thetab=='about'}><About/></div>

 </div>
<style>

</style>