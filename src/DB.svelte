<script>
	import { onMount } from 'svelte';
	import PouchDB from 'pouchdb-browser';
	export let documents;
	export let initsrc;
	export let collection;
	let db;

	$: if (db){ load_books(); }

	onMount(
		async () => {
			const pdb = new PouchDB(collection)
			await pdb.info();	
			db = pdb;
	});

	async function load_books(){
		if (!db) return;
		const r=await db.info();
		if (r.update_seq === 0) { //si il n'y a rien en base
			const res = await fetch(initsrc);
			const docs = await res.json();
			db.bulkDocs(docs); //on lance la construction de la bd avec les données lues
		}
		//requête de bd
		const zdocs = await db.allDocs({include_docs: true});
		console.log(zdocs);
		documents = zdocs.rows.map(d=>d.doc);
	}

</script>