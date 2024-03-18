<script>
    import Dropzone from "svelte-file-dropzone";
    import { setAppStatusLoading } from "../store";
    

    let files = {
      accepted: [],
      rejected: []
    };

    let button = false
    function setTrue(){
      button = true
    }
    
  
    async function handleFilesSelect(e) {
      setAppStatusLoading();
      const { acceptedFiles, fileRejections } = e.detail;
      
      files.accepted = [...files.accepted, ...acceptedFiles];
      files.rejected = [...files.rejected, ...fileRejections];


      if(acceptedFiles.length > 0){
        
        const formDataFiles = [];
        files.accepted.forEach(file => {
            const formData = new FormData()
            formData.append(file)
            formDataFiles.append(formData)
        })
        
        const res = await fetch('api/upload', {
          method: 'POST',
          body: formDataFiles
        })

        if (!res.ok){
            setAppStatusError();
            return
        }

        const result = await res.json()
        console.log(result)
        setAppStatusChatMode(result)
      }
    }
  </script>

  {#if button === false} 
    <Dropzone 
    accept="application/pdf" on:drop={handleFilesSelect}
    >Arrastra y suelta aquí tu PDF </Dropzone>
  {/if}

  <button disabled={files.length === 0}>A por ellos</button>

  <ol>
    {#each files.accepted as item}
      <li>{item.name}</li>
    {/each}
  </ol>