---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-healthedufood
---

<html>
    <head>
        <meta charset="utf-8">
        
            <script>function neighbourhoodHighlight(params) {
  // console.log("in nieghbourhoodhighlight");
  allNodes = nodes.get({ returnType: "Object" });
  // originalNodes = JSON.parse(JSON.stringify(allNodes));
  // if something is selected:
  if (params.nodes.length > 0) {
    highlightActive = true;
    var i, j;
    var selectedNode = params.nodes[0];
    var degrees = 2;

    // mark all nodes as hard to read.
    for (let nodeId in allNodes) {
      // nodeColors[nodeId] = allNodes[nodeId].color;
      allNodes[nodeId].color = "rgba(200,200,200,0.5)";
      if (allNodes[nodeId].hiddenLabel === undefined) {
        allNodes[nodeId].hiddenLabel = allNodes[nodeId].label;
        allNodes[nodeId].label = undefined;
      }
    }
    var connectedNodes = network.getConnectedNodes(selectedNode);
    var allConnectedNodes = [];

    // get the second degree nodes
    for (i = 1; i < degrees; i++) {
      for (j = 0; j < connectedNodes.length; j++) {
        allConnectedNodes = allConnectedNodes.concat(
          network.getConnectedNodes(connectedNodes[j])
        );
      }
    }

    // all second degree nodes get a different color and their label back
    for (i = 0; i < allConnectedNodes.length; i++) {
      // allNodes[allConnectedNodes[i]].color = "pink";
      allNodes[allConnectedNodes[i]].color = "rgba(150,150,150,0.75)";
      if (allNodes[allConnectedNodes[i]].hiddenLabel !== undefined) {
        allNodes[allConnectedNodes[i]].label =
          allNodes[allConnectedNodes[i]].hiddenLabel;
        allNodes[allConnectedNodes[i]].hiddenLabel = undefined;
      }
    }

    // all first degree nodes get their own color and their label back
    for (i = 0; i < connectedNodes.length; i++) {
      // allNodes[connectedNodes[i]].color = undefined;
      allNodes[connectedNodes[i]].color = nodeColors[connectedNodes[i]];
      if (allNodes[connectedNodes[i]].hiddenLabel !== undefined) {
        allNodes[connectedNodes[i]].label =
          allNodes[connectedNodes[i]].hiddenLabel;
        allNodes[connectedNodes[i]].hiddenLabel = undefined;
      }
    }

    // the main node gets its own color and its label back.
    // allNodes[selectedNode].color = undefined;
    allNodes[selectedNode].color = nodeColors[selectedNode];
    if (allNodes[selectedNode].hiddenLabel !== undefined) {
      allNodes[selectedNode].label = allNodes[selectedNode].hiddenLabel;
      allNodes[selectedNode].hiddenLabel = undefined;
    }
  } else if (highlightActive === true) {
    // console.log("highlightActive was true");
    // reset all nodes
    for (let nodeId in allNodes) {
      // allNodes[nodeId].color = "purple";
      allNodes[nodeId].color = nodeColors[nodeId];
      // delete allNodes[nodeId].color;
      if (allNodes[nodeId].hiddenLabel !== undefined) {
        allNodes[nodeId].label = allNodes[nodeId].hiddenLabel;
        allNodes[nodeId].hiddenLabel = undefined;
      }
    }
    highlightActive = false;
  }

  // transform the object into an array
  var updateArray = [];
  if (params.nodes.length > 0) {
    for (let nodeId in allNodes) {
      if (allNodes.hasOwnProperty(nodeId)) {
        // console.log(allNodes[nodeId]);
        updateArray.push(allNodes[nodeId]);
      }
    }
    nodes.update(updateArray);
  } else {
    // console.log("Nothing was selected");
    for (let nodeId in allNodes) {
      if (allNodes.hasOwnProperty(nodeId)) {
        // console.log(allNodes[nodeId]);
        // allNodes[nodeId].color = {};
        updateArray.push(allNodes[nodeId]);
      }
    }
    nodes.update(updateArray);
  }
}

function filterHighlight(params) {
  allNodes = nodes.get({ returnType: "Object" });
  // if something is selected:
  if (params.nodes.length > 0) {
    filterActive = true;
    let selectedNodes = params.nodes;

    // hiding all nodes and saving the label
    for (let nodeId in allNodes) {
      allNodes[nodeId].hidden = true;
      if (allNodes[nodeId].savedLabel === undefined) {
        allNodes[nodeId].savedLabel = allNodes[nodeId].label;
        allNodes[nodeId].label = undefined;
      }
    }

    for (let i=0; i < selectedNodes.length; i++) {
      allNodes[selectedNodes[i]].hidden = false;
      if (allNodes[selectedNodes[i]].savedLabel !== undefined) {
        allNodes[selectedNodes[i]].label = allNodes[selectedNodes[i]].savedLabel;
        allNodes[selectedNodes[i]].savedLabel = undefined;
      }
    }

  } else if (filterActive === true) {
    // reset all nodes
    for (let nodeId in allNodes) {
      allNodes[nodeId].hidden = false;
      if (allNodes[nodeId].savedLabel !== undefined) {
        allNodes[nodeId].label = allNodes[nodeId].savedLabel;
        allNodes[nodeId].savedLabel = undefined;
      }
    }
    filterActive = false;
  }

  // transform the object into an array
  var updateArray = [];
  if (params.nodes.length > 0) {
    for (let nodeId in allNodes) {
      if (allNodes.hasOwnProperty(nodeId)) {
        updateArray.push(allNodes[nodeId]);
      }
    }
    nodes.update(updateArray);
  } else {
    for (let nodeId in allNodes) {
      if (allNodes.hasOwnProperty(nodeId)) {
        updateArray.push(allNodes[nodeId]);
      }
    }
    nodes.update(updateArray);
  }
}

function selectNode(nodes) {
  network.selectNodes(nodes);
  neighbourhoodHighlight({ nodes: nodes });
  return nodes;
}

function selectNodes(nodes) {
  network.selectNodes(nodes);
  filterHighlight({nodes: nodes});
  return nodes;
}

function highlightFilter(filter) {
  let selectedNodes = []
  let selectedProp = filter['property']
  if (filter['item'] === 'node') {
    let allNodes = nodes.get({ returnType: "Object" });
    for (let nodeId in allNodes) {
      if (allNodes[nodeId][selectedProp] && filter['value'].includes((allNodes[nodeId][selectedProp]).toString())) {
        selectedNodes.push(nodeId)
      }
    }
  }
  else if (filter['item'] === 'edge'){
    let allEdges = edges.get({returnType: 'object'});
    // check if the selected property exists for selected edge and select the nodes connected to the edge
    for (let edge in allEdges) {
      if (allEdges[edge][selectedProp] && filter['value'].includes((allEdges[edge][selectedProp]).toString())) {
        selectedNodes.push(allEdges[edge]['from'])
        selectedNodes.push(allEdges[edge]['to'])
      }
    }
  }
  selectNodes(selectedNodes)
}</script>
            <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/vis-network/9.1.2/dist/dist/vis-network.min.css" integrity="sha512-WgxfT5LWjfszlPHXRmBWHkV2eceiWTOBvrKCNbdgDYTHrT2AeLCGbF4sZlZw3UMN3WtL0tGUoIAKsu8mllg/XA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
            <script src="https://cdnjs.cloudflare.com/ajax/libs/vis-network/9.1.2/dist/vis-network.min.js" integrity="sha512-LnvoEWDFrqGHlHmDD2101OrLcbsfkrzoSpvtSQtxK3RMnRV0eOkhhBN2dXHKRrUU8p2DGRTk35n4O8nWSVe1mQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
            
            
            
            
            
            
                <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/tom-select/2.0.0-rc.4/css/tom-select.min.css" integrity="sha512-43fHB3GLgZfz8QXl1RPQ8O66oIgv3po9cJ5erMt1c4QISq9dYb195T3vr5ImnJPXuVroKcGBPXBFKETW8jrPNQ==" crossorigin="anonymous" referrerpolicy="no-referrer" />
                <script src="https://cdnjs.cloudflare.com/ajax/libs/tom-select/2.0.0-rc.4/js/tom-select.complete.js" integrity="sha512-jeF9CfnvzDiw9G9xiksVjxR2lib44Gnovvkv+3CgCG6NXCD4gqlA5nDAVW5WjpA+i+/zKsUWV5xNEbW1X/HH0Q==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
            

        
<center>
<h1></h1>
</center>

<!-- <link rel="stylesheet" href="../node_modules/vis/dist/vis.min.css" type="text/css" />
<script type="text/javascript" src="../node_modules/vis/dist/vis.js"> </script>-->
        <link
          href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/css/bootstrap.min.css"
          rel="stylesheet"
          integrity="sha384-eOJMYsd53ii+scO/bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6"
          crossorigin="anonymous"
        />
        <script
          src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/js/bootstrap.bundle.min.js"
          integrity="sha384-JEW9xMcG8R+pH31jmWH6WWP0WintQrMb4s7ZOdauHnUtxwoG2vI5DkLtS3qm9Ekf"
          crossorigin="anonymous"
        ></script>


        <center>
          <h1></h1>
        </center>
        <style type="text/css">

             #mynetwork {
                 width: 100%;
                 height: 750px;
                 background-color: #ffffff;
                 border: 1px solid lightgray;
                 position: relative;
                 float: left;
             }

             

             

             
        </style>
    </head>


    <body>
        <div class="card" style="width: 100%">
            
                <div id="select-menu" class="card-header">
                    <div class="row no-gutters">
                        <div class="col-10 pb-2">
                            <select
                            class="form-select"
                            aria-label="Default select example"
                            onchange="selectNode([value]);"
                            id="select-node"
                            placeholder="Select node..."
                            >
                                <option selected>Select a Node by ID</option>
                                
                                    <option value="_A01a - Tanaman Pangan (O)">_A01a - Tanaman Pangan (O)</option>
                                
                                    <option value="_A01b - Tanaman Hortikultura (O)">_A01b - Tanaman Hortikultura (O)</option>
                                
                                    <option value="_A01c - Tanaman Perkebunan (O)">_A01c - Tanaman Perkebunan (O)</option>
                                
                                    <option value="_A01d - Peternakan (O)">_A01d - Peternakan (O)</option>
                                
                                    <option value="_A01e - Jasa Pertanian dan Perburuan (O)">_A01e - Jasa Pertanian dan Perburuan (O)</option>
                                
                                    <option value="_A02 - Kehutanan dan Penebangan Kayu (O)">_A02 - Kehutanan dan Penebangan Kayu (O)</option>
                                
                                    <option value="_A03 - Perikanan (O)">_A03 - Perikanan (O)</option>
                                
                                    <option value="_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)">_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)</option>
                                
                                    <option value="_B02 - Pertambangan Batubara dan Lignit (O)">_B02 - Pertambangan Batubara dan Lignit (O)</option>
                                
                                    <option value="_B04 - Pertambangan dan Penggalian Lainnya (O)">_B04 - Pertambangan dan Penggalian Lainnya (O)</option>
                                
                                    <option value="_C01 - Industri Batubara dan Pengilangan Migas (O)">_C01 - Industri Batubara dan Pengilangan Migas (O)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (O)">_C02 - Industri Makanan dan Minuman (O)</option>
                                
                                    <option value="_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)">_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)</option>
                                
                                    <option value="_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)">_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)</option>
                                
                                    <option value="_C10 - Industri Barang Galian bukan Logam (O)">_C10 - Industri Barang Galian bukan Logam (O)</option>
                                
                                    <option value="_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)">_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)</option>
                                
                                    <option value="_D01 - Ketenagalistrikan (O)">_D01 - Ketenagalistrikan (O)</option>
                                
                                    <option value="_F - Konstruksi (O)">_F - Konstruksi (O)</option>
                                
                                    <option value="_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)">_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)</option>
                                
                                    <option value="_H02 - Angkutan Darat (O)">_H02 - Angkutan Darat (O)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (O)">_J - Informasi dan Komunikasi (O)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (O)">_K01 - Jasa Perantara Keuangan (O)</option>
                                
                                    <option value="_L - Real Estate (O)">_L - Real Estate (O)</option>
                                
                                    <option value="_M - Jasa Perusahaan (O)">_M - Jasa Perusahaan (O)</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)</option>
                                
                                    <option value="_R - Jasa lainnya (O)">_R - Jasa lainnya (O)</option>
                                
                                    <option value="_A01c - Tanaman Perkebunan (I)">_A01c - Tanaman Perkebunan (I)</option>
                                
                                    <option value="_A01d - Peternakan (I)">_A01d - Peternakan (I)</option>
                                
                                    <option value="_B02 - Pertambangan Batubara dan Lignit (I)">_B02 - Pertambangan Batubara dan Lignit (I)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (I)">_C02 - Industri Makanan dan Minuman (I)</option>
                                
                                    <option value="_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)">_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)</option>
                                
                                    <option value="_C09 - Industri Karet, Barang dari Karet dan Plastik (I)">_C09 - Industri Karet, Barang dari Karet dan Plastik (I)</option>
                                
                                    <option value="_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)">_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)</option>
                                
                                    <option value="_D01 - Ketenagalistrikan (I)">_D01 - Ketenagalistrikan (I)</option>
                                
                                    <option value="_F - Konstruksi (I)">_F - Konstruksi (I)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)</option>
                                
                                    <option value="_H02 - Angkutan Darat (I)">_H02 - Angkutan Darat (I)</option>
                                
                                    <option value="_H05 - Angkutan Udara (I)">_H05 - Angkutan Udara (I)</option>
                                
                                    <option value="_I01 - Penyediaan Akomodasi (I)">_I01 - Penyediaan Akomodasi (I)</option>
                                
                                    <option value="_I02 - Penyediaan Makan Minum (I)">_I02 - Penyediaan Makan Minum (I)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (I)">_J - Informasi dan Komunikasi (I)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (I)">_K01 - Jasa Perantara Keuangan (I)</option>
                                
                                    <option value="_L - Real Estate (I)">_L - Real Estate (I)</option>
                                
                                    <option value="_M - Jasa Perusahaan (I)">_M - Jasa Perusahaan (I)</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)</option>
                                
                                    <option value="_P - Jasa Pendidikan (I)">_P - Jasa Pendidikan (I)</option>
                                
                                    <option value="_Q - Jasa Kesehatan dan Kegiatan Sosial (I)">_Q - Jasa Kesehatan dan Kegiatan Sosial (I)</option>
                                
                                    <option value="_R - Jasa lainnya (I)">_R - Jasa lainnya (I)</option>
                                
                            </select>
                        </div>
                        <div class="col-2 pb-2">
                            <button type="button" class="btn btn-primary btn-block" onclick="neighbourhoodHighlight({nodes: []});">Reset Selection</button>
                        </div>
                    </div>
                </div>
            
            
            <div id="mynetwork" class="card-body"></div>
        </div>

        
        

        <script type="text/javascript">

              // initialize global variables.
              var edges;
              var nodes;
              var allNodes;
              var allEdges;
              var nodeColors;
              var originalNodes;
              var network;
              var container;
              var options, data;
              var filter = {
                  item : '',
                  property : '',
                  value : []
              };

              
                  new TomSelect("#select-node",{
                      create: false,
                      sortField: {
                          field: "text",
                          direction: "asc"
                      }
                  });
              

              

              // This method is responsible for drawing the graph, returns the drawn network
              function drawGraph() {
                  var container = document.getElementById('mynetwork');

                  

                  // parsing and collecting nodes and edges from the python
                  nodes = new vis.DataSet([{"color": "#F8AD3C", "id": "_A01a - Tanaman Pangan (O)", "label": "Tanaman Pangan\n393,97", "physics": false, "shape": "dot", "size": 12.463924010983497, "title": "Tanaman Pangan", "x": -482, "y": -574}, {"color": "#F8AD3C", "id": "_A01b - Tanaman Hortikultura (O)", "label": "Tanaman Hortikultura\n388,53", "physics": false, "shape": "dot", "size": 12.429930618730301, "title": "Tanaman Hortikultura", "x": -350, "y": -316}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan (O)", "label": "Tanaman Perkebunan\n937,23", "physics": false, "shape": "dot", "size": 15.861583718937174, "title": "Tanaman Perkebunan", "x": -635, "y": -499}, {"color": "#F8AD3C", "id": "_A01d - Peternakan (O)", "label": "Peternakan\n475,00", "physics": false, "shape": "dot", "size": 12.970730185984145, "title": "Peternakan", "x": -426, "y": -391}, {"color": "#00A39D", "id": "_A01e - Jasa Pertanian dan Perburuan (O)", "label": "Jasa Pertanian \u0026 Perburuan\n37,34", "physics": false, "shape": "dot", "size": 10.233540079335247, "title": "Jasa Pertanian dan Perburuan", "x": -708, "y": -689}, {"color": "#00A39D", "id": "_A02 - Kehutanan dan Penebangan Kayu (O)", "label": "Kehutanan \u0026 Penebangan \nKayu\n168,29", "physics": false, "shape": "dot", "size": 11.052523798876074, "title": "Kehutanan dan Penebangan Kayu", "x": -902, "y": -557}, {"color": "#F05D5E", "id": "_A03 - Perikanan (O)", "label": "Perikanan\n569,85", "physics": false, "shape": "dot", "size": 13.5639165884849, "title": "Perikanan", "x": -788, "y": -252}, {"color": "#00A39D", "id": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)", "label": "Pertambangan Minyak, Gas \n\u0026 Panas Bumi\n570,29", "physics": false, "shape": "dot", "size": 13.56667432453723, "title": "Pertambangan Minyak, Gas dan Panas Bumi", "x": -960, "y": -71}, {"color": "#00A39D", "id": "_B02 - Pertambangan Batubara dan Lignit (O)", "label": "Pertambangan Batubara \n\u0026 Lignit\n1857,95", "physics": false, "shape": "dot", "size": 21.619892600761666, "title": "Pertambangan Batubara dan Lignit", "x": -1054, "y": -301}, {"color": "#F05D5E", "id": "_B04 - Pertambangan dan Penggalian Lainnya (O)", "label": "Pertambangan \u0026 Penggalian \nLainnya\n350,00", "physics": false, "shape": "dot", "size": 12.188961696298634, "title": "Pertambangan dan Penggalian Lainnya", "x": -881, "y": 311}, {"color": "#00A39D", "id": "_C01 - Industri Batubara dan Pengilangan Migas (O)", "label": "Industri Batubara \n\u0026 Pengilangan Migas\n750,54", "physics": false, "shape": "dot", "size": 14.69398043198059, "title": "Industri Batubara dan Pengilangan Migas", "x": -1043, "y": -137}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (O)", "label": "Industri Makanan \u0026 Minuman\n3748,51", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": -986, "y": -217}, {"color": "#00A39D", "id": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "label": "Industri Kertas, Percetakan \n\u0026 Media Rekaman\n408,57", "physics": false, "shape": "dot", "size": 12.555281797048966, "title": "Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "x": -506, "y": -306}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\n1089,76", "physics": false, "shape": "dot", "size": 16.81551483178048, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": -676, "y": -130}, {"color": "#F8AD3C", "id": "_C10 - Industri Barang Galian bukan Logam (O)", "label": "Industri Barang Galian \nbukan Logam\n303,72", "physics": false, "shape": "dot", "size": 11.899531618665552, "title": "Industri Barang Galian bukan Logam", "x": -653, "y": 380}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\n861,78", "physics": false, "shape": "dot", "size": 15.38970085412097, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": -757, "y": 342}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan (O)", "label": "Ketenagalistrikan\n924,09", "physics": false, "shape": "dot", "size": 15.779412103262658, "title": "Ketenagalistrikan", "x": -1051, "y": 222}, {"color": "#F8AD3C", "id": "_F - Konstruksi (O)", "label": "Konstruksi\n4796,82", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": -467, "y": 280}, {"color": "#F8AD3C", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "label": "Perdagangan Mobil \u0026 Motor\n628,54", "physics": false, "shape": "dot", "size": 13.930971467453958, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya", "x": -961, "y": 49}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\n3321,83", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": -439, "y": -76}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (O)", "label": "Angkutan Darat\n1209,78", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": -966, "y": 268}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (O)", "label": "Informasi \u0026 Komunikasi\n1517,46", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": -750, "y": 195}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (O)", "label": "Jasa Perantara Keuangan\n772,69", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": -994, "y": -383}, {"color": "#00A39D", "id": "_L - Real Estate (O)", "label": "Real Estate\n1090,32", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": -1047, "y": -3}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (O)", "label": "Jasa Perusahaan\n1091,25", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": -553, "y": 133}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\n988,14", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": -1048, "y": 113}, {"color": "#00A39D", "id": "_R - Jasa lainnya (O)", "label": "Jasa lainnya\n944,75", "physics": false, "shape": "dot", "size": 15.908606308830718, "title": "Jasa lainnya", "x": -973, "y": 154}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan (I)", "label": "Tanaman Perkebunan\n937,23", "physics": false, "shape": "dot", "size": 15.861583718937174, "title": "Tanaman Perkebunan", "x": 743, "y": -568}, {"color": "#F8AD3C", "id": "_A01d - Peternakan (I)", "label": "Peternakan\n475,00", "physics": false, "shape": "dot", "size": 12.970730185984145, "title": "Peternakan", "x": 525, "y": -627}, {"color": "#00A39D", "id": "_B02 - Pertambangan Batubara dan Lignit (I)", "label": "Pertambangan Batubara \n\u0026 Lignit\n1857,95", "physics": false, "shape": "dot", "size": 21.619892600761666, "title": "Pertambangan Batubara dan Lignit", "x": 759, "y": 262}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (I)", "label": "Industri Makanan \u0026 Minuman\n3748,51", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": -2, "y": -349}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\n1089,76", "physics": false, "shape": "dot", "size": 16.81551483178048, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": 556, "y": -443}, {"color": "#00A39D", "id": "_C09 - Industri Karet, Barang dari Karet dan Plastik (I)", "label": "Industri Karet \u0026 Plastik\n291,42", "physics": false, "shape": "dot", "size": 11.822602799809339, "title": "Industri Karet, Barang dari Karet dan Plastik", "x": 775, "y": -484}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\n861,78", "physics": false, "shape": "dot", "size": 15.38970085412097, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": 823, "y": -298}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan (I)", "label": "Ketenagalistrikan\n924,09", "physics": false, "shape": "dot", "size": 15.779412103262658, "title": "Ketenagalistrikan", "x": 798, "y": -397}, {"color": "#F8AD3C", "id": "_F - Konstruksi (I)", "label": "Konstruksi\n4796,82", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": 391, "y": -3}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\n3321,83", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": 712, "y": -124}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (I)", "label": "Angkutan Darat\n1209,78", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": 341, "y": 213}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara (I)", "label": "Angkutan Udara\n776,17", "physics": false, "shape": "dot", "size": 14.85425398172945, "title": "Angkutan Udara", "x": 423, "y": 341}, {"color": "#F8AD3C", "id": "_I01 - Penyediaan Akomodasi (I)", "label": "Penyediaan Akomodasi\n212,01", "physics": false, "shape": "dot", "size": 11.32591423032543, "title": "Penyediaan Akomodasi", "x": 385, "y": -483}, {"color": "#F8AD3C", "id": "_I02 - Penyediaan Makan Minum (I)", "label": "Penyediaan Makan Minum\n1176,87", "physics": false, "shape": "dot", "size": 17.36028160789432, "title": "Penyediaan Makan Minum", "x": -3, "y": -111}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (I)", "label": "Informasi \u0026 Komunikasi\n1517,46", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": 854, "y": -109}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (I)", "label": "Jasa Perantara Keuangan\n772,69", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": 456, "y": -363}, {"color": "#00A39D", "id": "_L - Real Estate (I)", "label": "Real Estate\n1090,32", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": 810, "y": 66}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (I)", "label": "Jasa Perusahaan\n1091,25", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": 660, "y": 136}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\n988,14", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": 691, "y": -287}, {"color": "#00A39D", "id": "_P - Jasa Pendidikan (I)", "label": "Jasa Pendidikan\n864,45", "physics": false, "shape": "dot", "size": 15.406420288740517, "title": "Jasa Pendidikan", "x": 0, "y": 107}, {"color": "#00A39D", "id": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "label": "Jasa Kesehatan \u0026 Kegiatan Sosial\n593,16", "physics": false, "shape": "dot", "size": 13.709689616148085, "title": "Jasa Kesehatan dan Kegiatan Sosial", "x": -3, "y": 356}, {"color": "#00A39D", "id": "_R - Jasa lainnya (I)", "label": "Jasa lainnya\n944,75", "physics": false, "shape": "dot", "size": 15.908606308830718, "title": "Jasa lainnya", "x": 710, "y": -639}]);
                  edges = new vis.DataSet([{"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_A01c - Tanaman Perkebunan (I)", "width": 0.7471748583014416}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "width": 1.1926562061939747}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik (I)", "width": 1.1934475607606052}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "width": 0.7741519109987682}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_F - Konstruksi (I)", "width": 0.7240415298316635}, {"from": "_H02 - Angkutan Darat (I)", "to": "_B02 - Pertambangan Batubara dan Lignit (I)", "width": 0.6880278322928477}, {"from": "_H02 - Angkutan Darat (I)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 0.6680660867921429}, {"from": "_H02 - Angkutan Darat (I)", "to": "_F - Konstruksi (I)", "width": 2.0667535927104854}, {"from": "_H02 - Angkutan Darat (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 1.0854183033634928}, {"from": "_H02 - Angkutan Darat (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.8943880221569857}, {"from": "_H05 - Angkutan Udara (I)", "to": "_F - Konstruksi (I)", "width": 0.6555058801999826}, {"from": "_H05 - Angkutan Udara (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6771637747649114}, {"from": "_H05 - Angkutan Udara (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.5517602151200103}, {"from": "_H05 - Angkutan Udara (I)", "to": "_M - Jasa Perusahaan (I)", "width": 0.5481240803178091}, {"from": "_H05 - Angkutan Udara (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.9677213739982339}, {"from": "_P - Jasa Pendidikan (I)", "to": "_H02 - Angkutan Darat (I)", "width": 0.509089946493233}, {"from": "_P - Jasa Pendidikan (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.5113815905621701}, {"from": "_P - Jasa Pendidikan (I)", "to": "_K01 - Jasa Perantara Keuangan (I)", "width": 0.5185003846798133}, {"from": "_P - Jasa Pendidikan (I)", "to": "_M - Jasa Perusahaan (I)", "width": 0.5272082888410693}, {"from": "_P - Jasa Pendidikan (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.5335771949486942}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "width": 0.5188898946028853}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_H02 - Angkutan Darat (I)", "width": 0.516187177414557}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.5159291672325472}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.5151107108757592}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.5827186888550925}, {"from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 1.1291470474880043}, {"from": "_B02 - Pertambangan Batubara dan Lignit (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 0.7400903965903549}, {"from": "_C02 - Industri Makanan dan Minuman (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 1.0547484279835253}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 1.1926562061939747}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 0.761301539286783}, {"from": "_H02 - Angkutan Darat (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0854183033634928}, {"from": "_J - Informasi dan Komunikasi (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0898145472209948}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.3811815420624}, {"from": "_L - Real Estate (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0515620830214112}, {"from": "_M - Jasa Perusahaan (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 0.967002797710794}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "to": "_M - Jasa Perusahaan (O)", "width": 0.6435803006535672}, {"from": "_J - Informasi dan Komunikasi (O)", "to": "_M - Jasa Perusahaan (O)", "width": 1.192510798986007}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_M - Jasa Perusahaan (O)", "width": 0.6012329188073154}, {"from": "_M - Jasa Perusahaan (O)", "to": "_M - Jasa Perusahaan (O)", "width": 0.7816539380799963}, {"from": "_C02 - Industri Makanan dan Minuman (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.7480328324471096}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.6682340145092815}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.6432746668433804}, {"from": "_M - Jasa Perusahaan (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.7467961369107794}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 0.7081321125298151}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "to": "_D01 - Ketenagalistrikan (I)", "width": 0.6639017488660136}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "to": "_F - Konstruksi (I)", "width": 0.7739515014169969}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 1.3811815420624}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "to": "_L - Real Estate (I)", "width": 0.6495561046174463}, {"from": "_M - Jasa Perusahaan (I)", "to": "_F - Konstruksi (I)", "width": 1.6110530576798139}, {"from": "_M - Jasa Perusahaan (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.967002797710794}, {"from": "_M - Jasa Perusahaan (I)", "to": "_J - Informasi dan Komunikasi (I)", "width": 1.041933607530739}, {"from": "_M - Jasa Perusahaan (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.9087898307632679}, {"from": "_A02 - Kehutanan dan Penebangan Kayu (O)", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.6152045824936789}, {"from": "_B02 - Pertambangan Batubara dan Lignit (O)", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.6355539322413619}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 1.1150518324416105}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.6441869827882712}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.6619577218138464}, {"from": "_B04 - Pertambangan dan Penggalian Lainnya (O)", "to": "_F - Konstruksi (O)", "width": 1.948462648929427}, {"from": "_C10 - Industri Barang Galian bukan Logam (O)", "to": "_F - Konstruksi (O)", "width": 2.3558137024076142}, {"from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)", "to": "_F - Konstruksi (O)", "width": 1.6688608155247535}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_F - Konstruksi (O)", "width": 3.3671944622889494}, {"from": "_H02 - Angkutan Darat (O)", "to": "_F - Konstruksi (O)", "width": 2.0667535927104854}, {"from": "_D01 - Ketenagalistrikan (O)", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.8520789400516859}, {"from": "_J - Informasi dan Komunikasi (O)", "to": "_J - Informasi dan Komunikasi (O)", "width": 1.9569666265721588}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.6205309787056978}, {"from": "_R - Jasa lainnya (O)", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.733076390749003}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "to": "_P - Jasa Pendidikan (I)", "width": 0.722728460697198}, {"from": "_F - Konstruksi (O)", "to": "_P - Jasa Pendidikan (I)", "width": 0.6343254598812026}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_P - Jasa Pendidikan (I)", "width": 0.6501144046032448}, {"from": "_J - Informasi dan Komunikasi (O)", "to": "_P - Jasa Pendidikan (I)", "width": 0.6395151780208141}, {"from": "_M - Jasa Perusahaan (O)", "to": "_P - Jasa Pendidikan (I)", "width": 0.8119143499771777}, {"from": "_A01d - Peternakan (I)", "to": "_A01d - Peternakan (I)", "width": 0.5137053604177713}, {"from": "_A01d - Peternakan (I)", "to": "_I01 - Penyediaan Akomodasi (I)", "width": 0.52359442184878}, {"from": "_A01d - Peternakan (I)", "to": "_R - Jasa lainnya (I)", "width": 0.5245391231892644}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_A01d - Peternakan (I)", "width": 1.4002388832707653}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 4.5}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "width": 1.0547484279835253}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_I01 - Penyediaan Akomodasi (I)", "width": 0.796879799386086}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 3.3337384873048705}, {"from": "_I01 - Penyediaan Akomodasi (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.516929450973239}, {"from": "_I01 - Penyediaan Akomodasi (I)", "to": "_M - Jasa Perusahaan (I)", "width": 0.5132431566168264}, {"from": "_I01 - Penyediaan Akomodasi (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.6001534379095229}, {"from": "_I01 - Penyediaan Akomodasi (I)", "to": "_R - Jasa lainnya (I)", "width": 0.5666164281372141}, {"from": "_I02 - Penyediaan Makan Minum (I)", "to": "_F - Konstruksi (I)", "width": 0.6945552318755811}, {"from": "_I02 - Penyediaan Makan Minum (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6625990486840323}, {"from": "_I02 - Penyediaan Makan Minum (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.5922870087643385}, {"from": "_I02 - Penyediaan Makan Minum (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.8207141010689653}, {"from": "_I02 - Penyediaan Makan Minum (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.5907374627513217}, {"from": "_A01a - Tanaman Pangan (O)", "to": "_A01a - Tanaman Pangan (O)", "width": 0.649989324716319}, {"from": "_A01e - Jasa Pertanian dan Perburuan (O)", "to": "_A01a - Tanaman Pangan (O)", "width": 0.5462743316942176}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_A01a - Tanaman Pangan (O)", "width": 0.6525397416606734}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_A01a - Tanaman Pangan (O)", "width": 0.5731496817573244}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_A01a - Tanaman Pangan (O)", "width": 0.5509021556365126}, {"from": "_A01c - Tanaman Perkebunan (O)", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.7545350777184326}, {"from": "_A01e - Jasa Pertanian dan Perburuan (O)", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.6677899575666557}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.7471748583014416}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.6058261129649384}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.6206657076426159}, {"from": "_A01a - Tanaman Pangan (O)", "to": "_A01d - Peternakan (O)", "width": 0.5}, {"from": "_A01d - Peternakan (O)", "to": "_A01d - Peternakan (O)", "width": 0.5137053604177713}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_A01d - Peternakan (O)", "width": 0.6732360441059627}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_A01d - Peternakan (O)", "width": 0.5304951637519888}, {"from": "_A01a - Tanaman Pangan (O)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 2.7120936943843525}, {"from": "_A01c - Tanaman Perkebunan (O)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 4.196915978386333}, {"from": "_A01d - Peternakan (O)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 2.04840686248141}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 1.7258516650023086}, {"from": "_F - Konstruksi (I)", "to": "_F - Konstruksi (I)", "width": 1.0616247596655155}, {"from": "_F - Konstruksi (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6802703173892923}, {"from": "_F - Konstruksi (I)", "to": "_L - Real Estate (I)", "width": 1.2424770178167863}, {"from": "_F - Konstruksi (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.9829672873531171}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "width": 1.033250393879516}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.8309612096213734}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "to": "_J - Informasi dan Komunikasi (I)", "width": 0.6205309787056978}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "to": "_L - Real Estate (I)", "width": 0.5612635228274067}, {"from": "_A01b - Tanaman Hortikultura (O)", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5561531095620559}, {"from": "_A01e - Jasa Pertanian dan Perburuan (O)", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5169858930023474}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.6169138482903446}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5678911989174869}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5288498327589529}, {"from": "_A03 - Perikanan (O)", "to": "_A03 - Perikanan (O)", "width": 0.589593168389115}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas (O)", "to": "_A03 - Perikanan (O)", "width": 0.5325120111202104}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_A03 - Perikanan (O)", "width": 0.5540944202173345}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_A03 - Perikanan (O)", "width": 0.5548322227395414}, {"from": "_A01b - Tanaman Hortikultura (O)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 0.8204249861337758}, {"from": "_A01d - Peternakan (O)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 0.7344016229141944}, {"from": "_A03 - Perikanan (O)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 1.0269932489381723}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 1.223568206822948}]);

                  nodeColors = {};
                  allNodes = nodes.get({ returnType: "Object" });
                  for (nodeId in allNodes) {
                    nodeColors[nodeId] = allNodes[nodeId].color;
                  }
                  allEdges = edges.get({ returnType: "Object" });
                  // adding nodes and edges to the graph
                  data = {nodes: nodes, edges: edges};

                  var options = {"nodes": {"borderWidth": 1, "borderWidthSelected": 2, "opacity": 100, "font": {"strokeWidth": 5}, "size": 25}, "edges": {"arrows": {"to": {"enabled": true, "scaleFactor": 0.5}}, "color": {"inherit": true}, "font": {"strokeWidth": 5}, "selfReferenceSize": 20, "selfReference": {"angle": 0.7853981633974483}, "smooth": {"forceDirection": "none"}}, "physics": {"barnesHut": {"springLength": 100}, "minVelocity": 0.75}};

                  


                  

                  network = new vis.Network(container, data, options);

                  

                  
                    network.on("selectNode", neighbourhoodHighlight);
                  

                  


                  

                  return network;

              }
              drawGraph();
        </script>
    </body>
</html>