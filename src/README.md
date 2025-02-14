<script>
// We want to immediately break into a hand-crafted home page, not bound to the
// style of the rest of the MDBook, unless we're rendering the 'print-version'
// of our docs.
if (!window.location.href.endsWith("print.html")) {
    // Redirect to home page.
    window.location.replace("home.html")
} else {
    // We're on the print-version page.
    //
    // Well, because of the way MDBook works we'd have a blank page here anyways.
    // May as well put something there.
    var header = document.createElement("span");
    var main = document.getElementsByTagName("main")[0]; 
    header.innerHTML = '<h1>Recaf</h1><p>An easy to use modern Java bytecode editor that abstracts away the complexities of Java programs.</p><center><img src="../assets/class-decomp-view.png"/></center><ul><li>GitHub: <a href="https://github.com/Col-E/Recaf/">GitHub.com/Col-E/Recaf</a></li></ul>';
    main.prepend(header);
}
</script>