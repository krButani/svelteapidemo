#  CURD OPERATION USING SVELTE
Curd Operation using svelte

# Setup Api in xampp
    - extract file /phpapifiles/apidemo.zip
    - create database with name `apidemo`
        - if you have another name then you must change the file inside apidemo/app/conf/appconf.php
    - copy `apidemo` folder into htdocs folder
    - browser and check it working fine or not links is following:
        - http://localhost/apidemo/

# Work on Project


## Create Layout

- routes/__layout.svelte file code

```
<slot></slot>
```

- routes/index.svelte file code

```
<script>
 <script>
  import {
    Col,
    Container,
    Row,
    FormGroup,
    Input,
    Alert,
    Label,
    Button,
  } from "sveltestrap";

  let succmsg = "";
  let errmsg = "";
  const handleSubmit = async (e) => {
    let stname = document.getElementById("stname").value;
    let stemail = document.getElementById("stemail").value;
    let stmobile = document.getElementById("stmobile").value;
    let ststate = document.getElementById("ststate").value;
    let stcity = document.getElementById("stcity").value;
    let stfile = document.getElementById("photo").files[0];

    const data = new FormData();
    data.append("uname", stname);
    data.append("email", stemail);
    data.append("mobileno", stmobile);
    data.append("state", ststate);
    data.append("city", stcity);
    data.append("propic", stfile);
    data.append("token", "anythings");

    const upload = fetch("http://localhost/apidemo/v1/create/", {
      method: "POST",
      body: data,
    })
      .then((response) => response.json())
      .then((result) => {
        console.log("Success:", result);

        if (result.status == 1) {
          succmsg = result.data.msg;
          document.querySelector('form').reset();
        } else {
          if (result.error.hasOwnProperty("uname")) {
            if (errmsg != "") errmsg += "<br>";
            errmsg += result.error.uname;
          }

          if (result.error.hasOwnProperty("mobileno")) {
            if (errmsg != "") errmsg += "<br>";
            errmsg += result.error.mobileno;
          }

          if (result.error.hasOwnProperty("email")) {
            if (errmsg != "") errmsg += "<br>";
            errmsg += result.error.email;
          }

          if (result.error.hasOwnProperty("propic")) {
            if (errmsg != "") errmsg += "<br>";
            errmsg += result.error.propic;
          }

          if (result.error.hasOwnProperty("msg")) {
            if (errmsg != "") errmsg += "<br>";
            errmsg += result.error.msg;
          }
        }

        
      })
      .catch((error) => {
        console.error("Error:", error);
        errmsg = error;
      });
  };
</script>

<form
  on:submit|preventDefault={handleSubmit}
  method="post"
  enctype="multipart/form-data"
>
  <Container class="mt-5">
    <Row>
        <Col>
            <h1>Add Student</h1>
        </Col>
    </Row>
    <Row>
      <Col>
        {#if succmsg != ""}
          <Alert color="success" dismissible>
            {succmsg}
          </Alert>
        {/if}

        {#if errmsg != ""}
          <Alert color="danger" dismissible>
            {errmsg}
          </Alert>
        {/if}
      </Col>
    </Row>
    <Row cols={2}>
      <Col>
        <FormGroup>
          <Label for="stname">Name</Label>
          <Input
            type="text"
            name="stname"
            id="stname"
            placeholder="Name"
            maxlength="20"
          />
        </FormGroup>
      </Col>
      <Col>
        <FormGroup>
          <Label for="stemail">E-Mail Address</Label>
          <Input
            type="text"
            name="stemail"
            id="stemail"
            placeholder="E-Mail Address"
            maxlength="250"
          />
        </FormGroup>
      </Col>
      <Col>
        <FormGroup>
          <Label for="stmobile">Mobile No.</Label>
          <Input
            type="number"
            name="stmobile"
            id="stmobile"
            placeholder="Mobile No."
            maxlength="10"
          />
        </FormGroup>
      </Col>

      <Col>
        <FormGroup>
          <Label for="ststate">State</Label>
          <Input
            type="text"
            name="ststate"
            placeholder="State"
            id="ststate"
            maxlength="50"
          />
        </FormGroup>
      </Col>
      <Col>
        <FormGroup>
          <Label for="stcity">City</Label>
          <Input
            type="text"
            name="stcity"
            placeholder="City"
            id="stcity"
            maxlength="50"
          />
        </FormGroup>
      </Col>
      <Col>
        <FormGroup>
          <Label for="photo">Photo</Label>
          <Input type="file" name="photo" id="photo" accept="image/*" />
        </FormGroup>
      </Col>
    </Row>
    <Row
      ><Col class="text-center">
        <Button type="submit" color="primary">Submit</Button>
        <Button type="button" color="dark"><a href="/view">View Data</a></Button>
      </Col></Row
    >
  </Container>
</form>

<style>
  a {
    text-decoration: none;
    color: #fff;
  }
</style>
```


- routes/view.svelte

```
<script context="module">

    
  import { Col, Container, Row, Button, Alert } from "sveltestrap";
  import jQuery from "jquery";
  import { onMount, tick } from "svelte";
  import initDt from "datatables.net-dt";
  initDt();

  
</script>

<script>

const loaddc = async () => {
    const data = new FormData();
    data.append("token", "anything");
    const response = await fetch("http://localhost/apidemo/v1/list/", {
      method: "POST",
      body: data,
    });

    const result = await response.json();
    let tdata = result.data.map((v) => {
      return {
        sname: v.sname,
        email: v.email,
        mobile: v.mobileno,
        state: v.state,
        city: v.city,
        propic: v.propic,
        sid: v.sid,
      };
    });

    return tdata;
  };
  let el; // table element
  let table; // table object (API)

  const dataPromise = loaddc();
    let tabdata = [];
  onMount( async () => {

    tabdata = await dataPromise;

    dataPromise.then(tick).then(() => {
      table = jQuery(el).DataTable();
      console.log(table);
      console.log(el);
    });
  });

  let succmsg = "";
  let errmsg = "";

  const handledelete = (e) => {
    const data = new FormData();
    data.append("ref", e);
    data.append("token", "anythings");

    const upload = fetch("http://localhost/apidemo/v1/delete/", {
      method: "POST",
      body: data,
    })
      .then((response) => response.json())
      .then(async (result)  => {
                  if (result.status == 1) {
            console.log(result);
          succmsg = result.data.msg;       
          document.getElementById(e).remove();
          
        } else {
          if (result.error.hasOwnProperty("error")) {
            if (errmsg != "") errmsg += "<br>";
            errmsg += result.error.error;
          }
        }
      })
      .catch((error) => {
        errmsg = error;
      });
  };
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="//cdn.datatables.net/1.10.21/css/jquery.dataTables.min.css"
  />
</svelte:head>
<Container class="mt-5">
    <Row>
        <Col>
            <h1>List Of Student</h1>
            <hr>
            <br>
        </Col>
    </Row>
    <Row>
        <Col>
          {#if succmsg != ""}
            <Alert color="success" dismissible>
              {succmsg}
            </Alert>
          {/if}
  
          {#if errmsg != ""}
            <Alert color="danger" dismissible>
              {errmsg}
            </Alert>
          {/if}
        </Col>
      </Row>
  <Row class="mb-5">
    <Col>
      <table bind:this={el} class="display" style="width:100%">
        <thead>
          <tr>
            <th>Name</th>
            <th>E-Mail Address</th>
            <th>Mobile No.</th>
            <th>State</th>
            <th>City</th>
            <th>Profile Pic</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
            {#each tabdata as row}
              <tr id="{row.sid}">
                {#each [row.sname, row.email, row.mobile, row.state, row.city, row.propic, row.sid] as cell, i}
                  {#if i == 5}
                    <td
                      ><img
                        src="http://localhost/apidemo/public/{cell}"
                        width="50px"
                      /></td
                    >
                  {:else if i == 6}
                    <td
                      ><Button
                        color="danger"
                        on:click={() => handledelete(cell)}>Delete</Button
                      ></td
                    >
                  {:else}
                    <td>{cell}</td>
                  {/if}
                {/each}
              </tr>
            {/each}
        </tbody>
      </table>
      
    </Col>
  </Row>
</Container>
```

## How to run Project

- First, Clone the repo.
- install required file for following:
    - npm install
- run command
    - npm run dev
- Goto Browser and type http://localhost:5000