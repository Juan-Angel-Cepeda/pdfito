<script>
    import Dropzone from "svelte-file-dropzone";
    import { setAppStatusLoading } from "../store";
    

    let files = {
      accepted: [],
      rejected: []
    };
  
    async function handleFilesSelect(e) {
      const { acceptedFiles, fileRejections } = e.detail;
      
      files.accepted = [...files.accepted, ...acceptedFiles];
      files.rejected = [...files.rejected, ...fileRejections];

      if(acceptedFiles.length > 0){
        
        setAppStatusLoading();
        const formData = new FormData();
        formData.append('file', acceptedFiles[0])
        
        const response = await fetch('api/upload', {
          method: 'POST',
          body: formData
        })

        if (!response.ok){
            setAppStatusError();
            return
        }

        const result = await res.json()
        console.log(result)
        setAppStatusChatMode(result)
      }
    }
  </script>

  

  {#if files.accepted.length === 0}
    <Dropzone 
    accept="application/pdf" 
    multiple={false} 
    on:drop={handleFilesSelect}>Arrastra y suelta aquí tu PDF </Dropzone>
  {/if}
  

  <ol>
    {#each files.accepted as item}
      <li>{item.name}</li>
    {/each}
  </ol>