---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-jasa-kesehatan
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
                                
                                    <option value="_A01c - Tanaman Perkebunan (O)">_A01c - Tanaman Perkebunan (O)</option>
                                
                                    <option value="_A01d - Peternakan (O)">_A01d - Peternakan (O)</option>
                                
                                    <option value="_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)">_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)</option>
                                
                                    <option value="_B02 - Pertambangan Batubara dan Lignit (O)">_B02 - Pertambangan Batubara dan Lignit (O)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (O)">_C02 - Industri Makanan dan Minuman (O)</option>
                                
                                    <option value="_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)">_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)</option>
                                
                                    <option value="_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)">_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)</option>
                                
                                    <option value="_H02 - Angkutan Darat (O)">_H02 - Angkutan Darat (O)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (O)">_J - Informasi dan Komunikasi (O)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (O)">_K01 - Jasa Perantara Keuangan (O)</option>
                                
                                    <option value="_L - Real Estate (O)">_L - Real Estate (O)</option>
                                
                                    <option value="_M - Jasa Perusahaan (O)">_M - Jasa Perusahaan (O)</option>
                                
                                    <option value="_A01c - Tanaman Perkebunan (I)">_A01c - Tanaman Perkebunan (I)</option>
                                
                                    <option value="_B02 - Pertambangan Batubara dan Lignit (I)">_B02 - Pertambangan Batubara dan Lignit (I)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (I)">_C02 - Industri Makanan dan Minuman (I)</option>
                                
                                    <option value="_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)">_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)</option>
                                
                                    <option value="_C09 - Industri Karet, Barang dari Karet dan Plastik (I)">_C09 - Industri Karet, Barang dari Karet dan Plastik (I)</option>
                                
                                    <option value="_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)">_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)</option>
                                
                                    <option value="_F - Konstruksi (I)">_F - Konstruksi (I)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)</option>
                                
                                    <option value="_H02 - Angkutan Darat (I)">_H02 - Angkutan Darat (I)</option>
                                
                                    <option value="_H05 - Angkutan Udara (I)">_H05 - Angkutan Udara (I)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (I)">_K01 - Jasa Perantara Keuangan (I)</option>
                                
                                    <option value="_M - Jasa Perusahaan (I)">_M - Jasa Perusahaan (I)</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)</option>
                                
                                    <option value="_P - Jasa Pendidikan (I)">_P - Jasa Pendidikan (I)</option>
                                
                                    <option value="_Q - Jasa Kesehatan dan Kegiatan Sosial (I)">_Q - Jasa Kesehatan dan Kegiatan Sosial (I)</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#F8AD3C", "id": "_A01a - Tanaman Pangan (O)", "label": "Tanaman Pangan\nRp393,97 T", "physics": false, "shape": "dot", "size": 12.463924010983497, "title": "Tanaman Pangan", "x": -562, "y": -426}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan (O)", "label": "Tanaman Perkebunan\nRp937,23 T", "physics": false, "shape": "dot", "size": 15.861583718937174, "title": "Tanaman Perkebunan", "x": -613, "y": -352}, {"color": "#F8AD3C", "id": "_A01d - Peternakan (O)", "label": "Peternakan\nRp475,00 T", "physics": false, "shape": "dot", "size": 12.970730185984145, "title": "Peternakan", "x": -475, "y": -475}, {"color": "#00A39D", "id": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)", "label": "Pertambangan Minyak, Gas \n\u0026 Panas Bumi\nRp570,29 T", "physics": false, "shape": "dot", "size": 13.56667432453723, "title": "Pertambangan Minyak, Gas dan Panas Bumi", "x": -652, "y": -257}, {"color": "#00A39D", "id": "_B02 - Pertambangan Batubara dan Lignit (O)", "label": "Pertambangan Batubara \n\u0026 Lignit\nRp1857,95 T", "physics": false, "shape": "dot", "size": 21.619892600761666, "title": "Pertambangan Batubara dan Lignit", "x": -708, "y": -140}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (O)", "label": "Industri Makanan \u0026 Minuman\nRp3748,51 T", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": -350, "y": -280}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\nRp1089,76 T", "physics": false, "shape": "dot", "size": 16.81551483178048, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": -451, "y": -125}, {"color": "#F8AD3C", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "label": "Perdagangan Mobil \u0026 Motor\nRp628,54 T", "physics": false, "shape": "dot", "size": 13.930971467453958, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya", "x": -641, "y": 310}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": -357, "y": 36}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (O)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": -713, "y": -24}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (O)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": -688, "y": 175}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (O)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": -554, "y": 440}, {"color": "#00A39D", "id": "_L - Real Estate (O)", "label": "Real Estate\nRp1090,32 T", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": -706, "y": 73}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (O)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": -350, "y": 347}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan (I)", "label": "Tanaman Perkebunan\nRp937,23 T", "physics": false, "shape": "dot", "size": 15.861583718937174, "title": "Tanaman Perkebunan", "x": 540, "y": -369}, {"color": "#00A39D", "id": "_B02 - Pertambangan Batubara dan Lignit (I)", "label": "Pertambangan Batubara \n\u0026 Lignit\nRp1857,95 T", "physics": false, "shape": "dot", "size": 21.619892600761666, "title": "Pertambangan Batubara dan Lignit", "x": 603, "y": -291}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (I)", "label": "Industri Makanan \u0026 Minuman\nRp3748,51 T", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": 665, "y": -174}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\nRp1089,76 T", "physics": false, "shape": "dot", "size": 16.81551483178048, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": 350, "y": -160}, {"color": "#00A39D", "id": "_C09 - Industri Karet, Barang dari Karet dan Plastik (I)", "label": "Industri Karet \u0026 Plastik\nRp291,42 T", "physics": false, "shape": "dot", "size": 11.822602799809339, "title": "Industri Karet, Barang dari Karet dan Plastik", "x": 353, "y": -359}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\nRp861,78 T", "physics": false, "shape": "dot", "size": 15.38970085412097, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": 445, "y": -412}, {"color": "#F8AD3C", "id": "_F - Konstruksi (I)", "label": "Konstruksi\nRp4796,82 T", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": 703, "y": -56}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": 726, "y": 64}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (I)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": 349, "y": -8}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara (I)", "label": "Angkutan Udara\nRp776,17 T", "physics": false, "shape": "dot", "size": 14.85425398172945, "title": "Angkutan Udara", "x": 450, "y": 121}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (I)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": 513, "y": 361}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (I)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": 624, "y": 292}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\nRp988,14 T", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": 697, "y": 189}, {"color": "#00A39D", "id": "_P - Jasa Pendidikan (I)", "label": "Jasa Pendidikan\nRp864,45 T", "physics": false, "shape": "dot", "size": 15.406420288740517, "title": "Jasa Pendidikan", "x": 361, "y": 246}, {"color": "#00A39D", "id": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "label": "Jasa Kesehatan \u0026 Kegiatan Sosial\nRp593,16 T", "physics": false, "shape": "dot", "size": 13.709689616148085, "title": "Jasa Kesehatan dan Kegiatan Sosial", "x": 0, "y": 0}]);
                  edges = new vis.DataSet([{"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_A01c - Tanaman Perkebunan (I)", "width": 0.7386271889029481}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "width": 1.1851231929921344}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik (I)", "width": 1.1859163499974499}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "width": 0.7656656862237506}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "to": "_F - Konstruksi (I)", "width": 0.7154411705165391}, {"from": "_H02 - Angkutan Darat (I)", "to": "_B02 - Pertambangan Batubara dan Lignit (I)", "width": 0.6793454459264336}, {"from": "_H02 - Angkutan Darat (I)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 0.6593382343049492}, {"from": "_H02 - Angkutan Darat (I)", "to": "_F - Konstruksi (I)", "width": 2.0612114784181133}, {"from": "_H02 - Angkutan Darat (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 1.0776410384031045}, {"from": "_H02 - Angkutan Darat (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.8861756546732449}, {"from": "_H05 - Angkutan Udara (I)", "to": "_F - Konstruksi (I)", "width": 0.6467494198002237}, {"from": "_H05 - Angkutan Udara (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6684566437411876}, {"from": "_H05 - Angkutan Udara (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.542767457100952}, {"from": "_H05 - Angkutan Udara (I)", "to": "_M - Jasa Perusahaan (I)", "width": 0.5391230404105721}, {"from": "_H05 - Angkutan Udara (I)", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.9596760351459253}, {"from": "_P - Jasa Pendidikan (I)", "to": "_H02 - Angkutan Darat (I)", "width": 0.5}, {"from": "_P - Jasa Pendidikan (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.502296863660882}, {"from": "_P - Jasa Pendidikan (I)", "to": "_K01 - Jasa Perantara Keuangan (I)", "width": 0.5094318719895092}, {"from": "_P - Jasa Pendidikan (I)", "to": "_M - Jasa Perusahaan (I)", "width": 0.5181596098182326}, {"from": "_P - Jasa Pendidikan (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.524543022144981}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "width": 0.5098222690847578}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_H02 - Angkutan Darat (I)", "width": 0.5071133960186225}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.5068547981764757}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.5060344776522696}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.5737964437932273}, {"from": "_A01a - Tanaman Pangan (O)", "to": "_C02 - Industri Makanan dan Minuman (O)", "width": 2.7080214470936177}, {"from": "_A01c - Tanaman Perkebunan (O)", "to": "_C02 - Industri Makanan dan Minuman (O)", "width": 4.1962256552513875}, {"from": "_A01d - Peternakan (O)", "to": "_C02 - Industri Makanan dan Minuman (O)", "width": 2.0428229605281096}, {"from": "_C02 - Industri Makanan dan Minuman (O)", "to": "_C02 - Industri Makanan dan Minuman (O)", "width": 4.5}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_C02 - Industri Makanan dan Minuman (O)", "width": 1.7195330911453852}, {"from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 1.121469381851825}, {"from": "_B02 - Pertambangan Batubara dan Lignit (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 0.7315265911785152}, {"from": "_C02 - Industri Makanan dan Minuman (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 1.0469013073956186}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 1.1851231929921344}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "width": 0.7527860457009644}, {"from": "_H02 - Angkutan Darat (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0776410384031045}, {"from": "_J - Informasi dan Komunikasi (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0820472954207383}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.3740779259636482}, {"from": "_L - Real Estate (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.043707705014814}, {"from": "_M - Jasa Perusahaan (O)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 0.9589558221841639}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "to": "_M - Jasa Perusahaan (O)", "width": 0.6347966778075181}, {"from": "_J - Informasi dan Komunikasi (O)", "to": "_M - Jasa Perusahaan (O)", "width": 1.1849774545956102}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "to": "_M - Jasa Perusahaan (O)", "width": 0.5923528429142294}, {"from": "_M - Jasa Perusahaan (O)", "to": "_M - Jasa Perusahaan (O)", "width": 0.7731848003913437}, {"from": "_C02 - Industri Makanan dan Minuman (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.7394871172242232}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.6595065445047656}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.6344903478666383}, {"from": "_M - Jasa Perusahaan (O)", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial (I)", "width": 0.7382476049127458}]);

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