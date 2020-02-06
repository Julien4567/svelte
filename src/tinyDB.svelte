<script>
    import {onMount} from 'svelte';
    import PouchDB from 'pouchdb-browser';
    export let documents;
    export let initsrc;
    export let collection;
    let db;

    $: if (db) {load_books();}

    export let elements;

    onMount(async()=>{
        const res=nex pouchDB();
    })
</script>