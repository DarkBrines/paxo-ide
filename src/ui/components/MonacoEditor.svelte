<script>
    import loader from '@monaco-editor/loader'
    import { onMount } from 'svelte'

    import { AutoCompleteProvider } from '../../editor/autocomplete'

    export let filePath
    export let code
    export let language

    let editor

    onMount(async () => {
        await loader.init()

        const monaco = window.monaco
        const theme = await window.darkMode.get() === 'dark' ? 'vs-dark' : 'vs'

        editor = monaco.editor.create(document.getElementById('editor-container'), {
            value: code,
            language: window.fs.fileTypes[language]
        })

        let autoComplete = new AutoCompleteProvider(monaco, editor)
        autoComplete.setTokens()
        autoComplete.setCompletion()
        autoComplete.setHovers()

        monaco.editor.setTheme(theme)
    })

    function save() {
        window.fs.editFile(filePath, editor.getValue())
    }

    document.addEventListener('keydown', (event) => {
        if((event.ctrlKey || event.metaKey) && event.key === 's') {
            save()
        }
    })

    $: {
        if (editor) {
            editor.setValue(code)
            monaco.editor.setModelLanguage(editor.getModel(), window.fs.fileTypes[language])
        }
    }
</script>

<div id="editor-container" style="height: 90vh;">

</div>
