---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-realestat-value
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
                                
                                    <option value="_B04 - Pertambangan dan Penggalian Lainnya (O)">_B04 - Pertambangan dan Penggalian Lainnya (O)</option>
                                
                                    <option value="_C10 - Industri Barang Galian bukan Logam (O)">_C10 - Industri Barang Galian bukan Logam (O)</option>
                                
                                    <option value="_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)">_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)</option>
                                
                                    <option value="_D01 - Ketenagalistrikan (O)">_D01 - Ketenagalistrikan (O)</option>
                                
                                    <option value="_F - Konstruksi (O)">_F - Konstruksi (O)</option>
                                
                                    <option value="_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)">_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)</option>
                                
                                    <option value="_H02 - Angkutan Darat (O)">_H02 - Angkutan Darat (O)</option>
                                
                                    <option value="_H05 - Angkutan Udara (O)">_H05 - Angkutan Udara (O)</option>
                                
                                    <option value="_I02 - Penyediaan Makan Minum (O)">_I02 - Penyediaan Makan Minum (O)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (O)">_J - Informasi dan Komunikasi (O)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (O)">_K01 - Jasa Perantara Keuangan (O)</option>
                                
                                    <option value="_K02 - Asuransi dan Dana Pensiun (O)">_K02 - Asuransi dan Dana Pensiun (O)</option>
                                
                                    <option value="_M - Jasa Perusahaan (O)">_M - Jasa Perusahaan (O)</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)</option>
                                
                                    <option value="_R - Jasa lainnya (O)">_R - Jasa lainnya (O)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (I)">_C02 - Industri Makanan dan Minuman (I)</option>
                                
                                    <option value="_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)">_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)</option>
                                
                                    <option value="_C14 - Industri Alat Angkutan (I)">_C14 - Industri Alat Angkutan (I)</option>
                                
                                    <option value="_F - Konstruksi (I)">_F - Konstruksi (I)</option>
                                
                                    <option value="_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)">_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)</option>
                                
                                    <option value="_H02 - Angkutan Darat (I)">_H02 - Angkutan Darat (I)</option>
                                
                                    <option value="_H05 - Angkutan Udara (I)">_H05 - Angkutan Udara (I)</option>
                                
                                    <option value="_I02 - Penyediaan Makan Minum (I)">_I02 - Penyediaan Makan Minum (I)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (I)">_J - Informasi dan Komunikasi (I)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (I)">_K01 - Jasa Perantara Keuangan (I)</option>
                                
                                    <option value="_L - Real Estate (I)">_L - Real Estate (I)</option>
                                
                                    <option value="_M - Jasa Perusahaan (I)">_M - Jasa Perusahaan (I)</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)</option>
                                
                                    <option value="_P - Jasa Pendidikan (I)">_P - Jasa Pendidikan (I)</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#F05D5E", "id": "_B04 - Pertambangan dan Penggalian Lainnya (O)", "label": "Pertambangan \u0026 Penggalian \nLainnya\nRp350,00 T", "physics": false, "shape": "dot", "size": 12.188961696298634, "title": "Pertambangan dan Penggalian Lainnya", "x": -612, "y": -410}, {"color": "#F8AD3C", "id": "_C10 - Industri Barang Galian bukan Logam (O)", "label": "Industri Barang Galian \nbukan Logam\nRp303,72 T", "physics": false, "shape": "dot", "size": 11.899531618665552, "title": "Industri Barang Galian bukan Logam", "x": -494, "y": -497}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\nRp861,78 T", "physics": false, "shape": "dot", "size": 15.38970085412097, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": -655, "y": -286}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan (O)", "label": "Ketenagalistrikan\nRp924,09 T", "physics": false, "shape": "dot", "size": 15.779412103262658, "title": "Ketenagalistrikan", "x": -672, "y": 334}, {"color": "#F8AD3C", "id": "_F - Konstruksi (O)", "label": "Konstruksi\nRp4796,82 T", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": -349, "y": -346}, {"color": "#F8AD3C", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "label": "Perdagangan Mobil \u0026 Motor\nRp628,54 T", "physics": false, "shape": "dot", "size": 13.930971467453958, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya", "x": -588, "y": 409}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": -769, "y": -12}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (O)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": -760, "y": 128}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara (O)", "label": "Angkutan Udara\nRp776,17 T", "physics": false, "shape": "dot", "size": 14.85425398172945, "title": "Angkutan Udara", "x": -472, "y": 491}, {"color": "#F8AD3C", "id": "_I02 - Penyediaan Makan Minum (O)", "label": "Penyediaan Makan Minum\nRp1176,87 T", "physics": false, "shape": "dot", "size": 17.36028160789432, "title": "Penyediaan Makan Minum", "x": -338, "y": 579}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (O)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": -292, "y": 37}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (O)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": -353, "y": -136}, {"color": "#00A39D", "id": "_K02 - Asuransi dan Dana Pensiun (O)", "label": "Asuransi \u0026 Dana Pensiun\nRp188,16 T", "physics": false, "shape": "dot", "size": 11.176762048428268, "title": "Asuransi dan Dana Pensiun", "x": -726, "y": -156}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (O)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": -398, "y": 274}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\nRp988,14 T", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": -132, "y": 507}, {"color": "#00A39D", "id": "_R - Jasa lainnya (O)", "label": "Jasa lainnya\nRp944,75 T", "physics": false, "shape": "dot", "size": 15.908606308830718, "title": "Jasa lainnya", "x": -725, "y": 232}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (I)", "label": "Industri Makanan \u0026 Minuman\nRp3748,51 T", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": 703, "y": -154}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\nRp861,78 T", "physics": false, "shape": "dot", "size": 15.38970085412097, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": 646, "y": -260}, {"color": "#F8AD3C", "id": "_C14 - Industri Alat Angkutan (I)", "label": "Industri Alat Angkutan\nRp768,96 T", "physics": false, "shape": "dot", "size": 14.809156700121282, "title": "Industri Alat Angkutan", "x": 511, "y": 266}, {"color": "#F8AD3C", "id": "_F - Konstruksi (I)", "label": "Konstruksi\nRp4796,82 T", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": 304, "y": -162}, {"color": "#F8AD3C", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "label": "Perdagangan Mobil \u0026 Motor\nRp628,54 T", "physics": false, "shape": "dot", "size": 13.930971467453958, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya", "x": 333, "y": 134}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": 447, "y": 10}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (I)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": 625, "y": 194}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara (I)", "label": "Angkutan Udara\nRp776,17 T", "physics": false, "shape": "dot", "size": 14.85425398172945, "title": "Angkutan Udara", "x": 452, "y": -501}, {"color": "#F8AD3C", "id": "_I02 - Penyediaan Makan Minum (I)", "label": "Penyediaan Makan Minum\nRp1176,87 T", "physics": false, "shape": "dot", "size": 17.36028160789432, "title": "Penyediaan Makan Minum", "x": 706, "y": 103}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (I)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": 407, "y": 354}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (I)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": 530, "y": -422}, {"color": "#00A39D", "id": "_L - Real Estate (I)", "label": "Real Estate\nRp1090,32 T", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": 0, "y": 0}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (I)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": 729, "y": -7}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\nRp988,14 T", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": 579, "y": -341}, {"color": "#00A39D", "id": "_P - Jasa Pendidikan (I)", "label": "Jasa Pendidikan\nRp864,45 T", "physics": false, "shape": "dot", "size": 15.406420288740517, "title": "Jasa Pendidikan", "x": 304, "y": -324}, {"color": "#00A39D", "id": "_R - Jasa lainnya (I)", "label": "Jasa lainnya\nRp944,75 T", "physics": false, "shape": "dot", "size": 15.908606308830718, "title": "Jasa lainnya", "x": 264, "y": 238}]);
                  edges = new vis.DataSet([{"from": "_F - Konstruksi (I)", "label": "Rp75,38 T", "to": "_F - Konstruksi (I)", "width": 1.2732884646010967}, {"from": "_F - Konstruksi (I)", "label": "Rp25,40 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.7395718840231444}, {"from": "_F - Konstruksi (I)", "label": "Rp65,08 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 1.1632050552958217}, {"from": "_F - Konstruksi (I)", "label": "Rp19,38 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.6752707260295595}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "label": "Rp41,55 T", "to": "_C14 - Industri Alat Angkutan (I)", "width": 0.9120176690580609}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "label": "Rp16,12 T", "to": "_F - Konstruksi (I)", "width": 0.6404397393913763}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "label": "Rp29,54 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.7837040285623628}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "label": "Rp67,28 T", "to": "_H02 - Angkutan Darat (I)", "width": 1.1867661445110125}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "label": "Rp20,59 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.6882231435793258}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Rp162,44 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 2.2028932452042547}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Rp71,67 T", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "width": 1.2335777183646521}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Rp45,15 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.9504681495715409}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Rp96,61 T", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 1.4999330064818142}, {"from": "_L - Real Estate (I)", "label": "Rp42,48 T", "to": "_F - Konstruksi (I)", "width": 0.9219801570888579}, {"from": "_L - Real Estate (I)", "label": "Rp14,30 T", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "width": 0.6210598461854044}, {"from": "_L - Real Estate (I)", "label": "Rp74,07 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 1.2592054573653688}, {"from": "_L - Real Estate (I)", "label": "Rp15,48 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.6336329016326698}, {"from": "_L - Real Estate (I)", "label": "Rp30,02 T", "to": "_R - Jasa lainnya (I)", "width": 0.788862073409107}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp2,97 T", "to": "_H02 - Angkutan Darat (I)", "width": 0.5}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp3,27 T", "to": "_H05 - Angkutan Udara (I)", "width": 0.5032072222079662}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp4,20 T", "to": "_K01 - Jasa Perantara Keuangan (I)", "width": 0.5131701806348539}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp5,34 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.5253571445658515}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp6,18 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.5342706130166043}, {"from": "_R - Jasa lainnya (I)", "label": "Rp14,56 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 0.6238002257970838}, {"from": "_R - Jasa lainnya (I)", "label": "Rp33,94 T", "to": "_F - Konstruksi (I)", "width": 0.8307298475862444}, {"from": "_R - Jasa lainnya (I)", "label": "Rp10,80 T", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 0.5836474754272297}, {"from": "_R - Jasa lainnya (I)", "label": "Rp32,32 T", "to": "_J - Informasi dan Komunikasi (I)", "width": 0.8134755122045081}, {"from": "_R - Jasa lainnya (I)", "label": "Rp15,44 T", "to": "_R - Jasa lainnya (I)", "width": 0.6331865548560666}, {"from": "_B04 - Pertambangan dan Penggalian Lainnya (O)", "label": "Rp191,62 T", "to": "_F - Konstruksi (O)", "width": 2.5144437608650043}, {"from": "_C10 - Industri Barang Galian bukan Logam (O)", "label": "Rp245,01 T", "to": "_F - Konstruksi (O)", "width": 3.0845433513130156}, {"from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)", "label": "Rp154,97 T", "to": "_F - Konstruksi (O)", "width": 2.123132901714243}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp377,56 T", "to": "_F - Konstruksi (O)", "width": 4.5}, {"from": "_H02 - Angkutan Darat (O)", "label": "Rp207,12 T", "to": "_F - Konstruksi (O)", "width": 2.6799953607135154}, {"from": "_D01 - Ketenagalistrikan (O)", "label": "Rp47,92 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.9800230245785289}, {"from": "_J - Informasi dan Komunikasi (O)", "label": "Rp192,73 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 2.5263453237305096}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp72,80 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 1.2457301272121724}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "label": "Rp17,57 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.6559649503320404}, {"from": "_R - Jasa lainnya (O)", "label": "Rp32,32 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.8134755122045081}, {"from": "_F - Konstruksi (O)", "label": "Rp10,91 T", "to": "_K01 - Jasa Perantara Keuangan (O)", "width": 0.5847770056728735}, {"from": "_J - Informasi dan Komunikasi (O)", "label": "Rp30,07 T", "to": "_K01 - Jasa Perantara Keuangan (O)", "width": 0.7894405649595129}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp19,35 T", "to": "_K01 - Jasa Perantara Keuangan (O)", "width": 0.6749699633342584}, {"from": "_K02 - Asuransi dan Dana Pensiun (O)", "label": "Rp15,74 T", "to": "_K01 - Jasa Perantara Keuangan (O)", "width": 0.6363849832698565}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp35,76 T", "to": "_K01 - Jasa Perantara Keuangan (O)", "width": 0.8502222362524409}, {"from": "_F - Konstruksi (O)", "label": "Rp99,09 T", "to": "_L - Real Estate (I)", "width": 1.526396434798499}, {"from": "_J - Informasi dan Komunikasi (O)", "label": "Rp44,29 T", "to": "_L - Real Estate (I)", "width": 0.9413030305613734}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp21,38 T", "to": "_L - Real Estate (I)", "width": 0.6965864542012474}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp37,50 T", "to": "_L - Real Estate (I)", "width": 0.8687696250534496}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "label": "Rp9,80 T", "to": "_L - Real Estate (I)", "width": 0.573018430285987}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "label": "Rp20,59 T", "to": "_M - Jasa Perusahaan (O)", "width": 0.6882231435793258}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp19,71 T", "to": "_M - Jasa Perusahaan (O)", "width": 0.6787734244092605}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp38,69 T", "to": "_M - Jasa Perusahaan (O)", "width": 0.88146119581058}, {"from": "_F - Konstruksi (O)", "label": "Rp65,08 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "width": 1.1632050552958217}, {"from": "_H02 - Angkutan Darat (O)", "label": "Rp53,47 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "width": 1.0392358096554533}, {"from": "_H05 - Angkutan Udara (O)", "label": "Rp63,08 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "width": 1.1418679582503857}, {"from": "_I02 - Penyediaan Makan Minum (O)", "label": "Rp43,81 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "width": 0.9361270245416113}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp55,35 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "width": 1.0593915576719288}]);

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