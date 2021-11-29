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
        <Button type="button" color="dark">View Data</Button>
      </Col></Row
    >
  </Container>
</form>
