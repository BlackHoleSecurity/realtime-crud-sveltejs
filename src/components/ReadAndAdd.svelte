<script>
    // import module firebase. ini sangat diperlukan
    import { FirebaseApp, User, Doc, Collection } from "sveltefire";
    import { Container,Row,Col,Card,CardBody,CardFooter,CardHeader,FormGroup } from "sveltestrap";
    import Swal from "sweetalert2";
    import firebase from "firebase/app";
    import "firebase/firestore";
    import "firebase/auth";
    import "firebase/performance";
    import "firebase/analytics";
    
    // konfig firebase 
    // di konfig firebase ini kalian bisa pake firestore kalian masing-masing
    // caranya buka firebase di google dan buat project dan copy semua konfigurasi 
    // yang ada di project kalian
    let firebaseConfig = {
        apiKey: "AIzaSyCNO2V9dYxv4yvonS5bn0TAsdGy0VOTjAs",
        authDomain: "crud-api-4c7ab.firebaseapp.com",
        databaseURL: "https://crud-api-4c7ab.firebaseio.com",
        projectId: "crud-api-4c7ab",
        storageBucket: "crud-api-4c7ab.appspot.com",
        messagingSenderId: "176451714422",
        appId: "1:176451714422:web:e8e4adebf58734041d8e57"
    };
    firebase.initializeApp(firebaseConfig);

    let title = "";
    let content = "";
    let data = "";
    let mode = "add";
    let updateID = "";

    function deleteBlog(id) {
        firebase.firestore().collection("blogs").doc(id).delete();
        Swal.fire({
            title: "Delete Data Success",
            text: "Data deleted successfully !!!",
            icon: "success",
        });
    }
    function addData() {
        try {
            if (title == undefined || content == undefined) {
                throw new Error();
            } else {
                firebase.firestore().collection("blogs").add({
                    title: title,
                    content: content,
                    createAt: Date.now(),
                });
                Swal.fire({
                    title: "Add Data Success",
                    text: "New data added successfully !!!",
                    icon: "success",
                });
                title = "";
                content = "";
            }
        } catch (err) {
            console.log(err);
        }
    }
    function updateData(id) {
        let dataTarget = firebase.firestore().collection("blogs").doc(id);
        dataTarget.update({
            title: title,
            content: content,
        });
        Swal.fire({
            title: "Update Data Success",
            text: "Data Is Up To Date !!!",
            icon: "success",
        });
    }

</script>
<FirebaseApp {firebase}>


    <Container class="mb-5">
        <Collection path={"blogs"} let:data={blogs} let:ref={blogsRef} log>
            <Row>
                <Col sm="8">
                    {#if !blogs.length}
                        <h2 class="mt-5 text-center">No Post Avaliable</h2>
                    {/if}
                    {#each blogs as blog}
                        <Card class="mb-3">
                            <CardHeader class="text-center">
                                <h4>{blog.title}</h4>
                            </CardHeader>
                            <CardBody>
                                <p>{blog.content}</p>
                            </CardBody>
                            {#if mode == "add"}
                                <CardFooter class="text-center">
                                    <button class="btn btn-danger" on:click={deleteBlog(blog.ref.id)}>Delete</button>
                                    <button class="btn btn-success" on:click={
                                        () => {
                                            mode = "update"
                                            title = blog.title
                                            content = blog.content
                                            updateID = blog.ref.id
                                        }
                                    }>Update</button>
                                </CardFooter>
                            {:else}
                                <CardFooter class="text-center">
                                    <button class="btn btn-warning" on:click={
                                        () => {
                                            mode = "add"
                                            title = ""
                                            content = ""
                                        }
                                    }>Cancel</button>
                                </CardFooter>
                            {/if}
                        </Card>
                    {/each}
                </Col>
                <Col sm="4">
                    {#if mode == "add"}
                        <Card>
                            <CardBody>
                                <form on:submit|preventDefault={addData}>
                                    <FormGroup>
                                        <label for="title">Title</label>
                                        <input type="text" class="form-control" bind:value={title} placeholder="Title Here..." required>
                                    </FormGroup>
                                    <FormGroup>
                                        <label for="content">Content</label>
                                        <textarea cols="30" rows="5" class="form-control" bind:value={content} placeholder="Content Here..." required></textarea>
                                    </FormGroup>
                                    <button class="btn btn-success" type="submit">Add Post</button>
                                </form>
                            </CardBody>
                        </Card>
                    {:else}
                        <Card>
                            <CardBody>
                                <form on:submit|preventDefault={updateData(updateID)}>
                                    <FormGroup>
                                        <label for="title">Title</label>
                                        <input type="text" class="form-control" bind:value={title} placeholder="Title Here..." required>
                                    </FormGroup>
                                    <FormGroup>
                                        <label for="content">Content</label>
                                        <textarea cols="30" rows="5" class="form-control" bind:value={content} placeholder="Content Here..." required></textarea>
                                    </FormGroup>
                                    <button class="btn btn-info" type="submit">Update Post</button>
                                </form>
                            </CardBody>
                        </Card>
                    {/if}
                </Col>
            </Row>
        </Collection>
    </Container>
</FirebaseApp>