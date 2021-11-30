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
