---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-jasa-pendidikan-value
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
                                
                                    <option value="_A02 - Kehutanan dan Penebangan Kayu (O)">_A02 - Kehutanan dan Penebangan Kayu (O)</option>
                                
                                    <option value="_B02 - Pertambangan Batubara dan Lignit (O)">_B02 - Pertambangan Batubara dan Lignit (O)</option>
                                
                                    <option value="_B04 - Pertambangan dan Penggalian Lainnya (O)">_B04 - Pertambangan dan Penggalian Lainnya (O)</option>
                                
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
                                
                                    <option value="_B02 - Pertambangan Batubara dan Lignit (I)">_B02 - Pertambangan Batubara dan Lignit (I)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (I)">_C02 - Industri Makanan dan Minuman (I)</option>
                                
                                    <option value="_D01 - Ketenagalistrikan (I)">_D01 - Ketenagalistrikan (I)</option>
                                
                                    <option value="_F - Konstruksi (I)">_F - Konstruksi (I)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)</option>
                                
                                    <option value="_H02 - Angkutan Darat (I)">_H02 - Angkutan Darat (I)</option>
                                
                                    <option value="_H05 - Angkutan Udara (I)">_H05 - Angkutan Udara (I)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (I)">_J - Informasi dan Komunikasi (I)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (I)">_K01 - Jasa Perantara Keuangan (I)</option>
                                
                                    <option value="_L - Real Estate (I)">_L - Real Estate (I)</option>
                                
                                    <option value="_M - Jasa Perusahaan (I)">_M - Jasa Perusahaan (I)</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)</option>
                                
                                    <option value="_P - Jasa Pendidikan (I)">_P - Jasa Pendidikan (I)</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#00A39D", "id": "_A02 - Kehutanan dan Penebangan Kayu (O)", "label": "Kehutanan \u0026 Penebangan \nKayu\nRp168,29 T", "physics": false, "shape": "dot", "size": 11.052523798876074, "title": "Kehutanan dan Penebangan Kayu", "x": -584, "y": -491}, {"color": "#00A39D", "id": "_B02 - Pertambangan Batubara dan Lignit (O)", "label": "Pertambangan Batubara \n\u0026 Lignit\nRp1857,95 T", "physics": false, "shape": "dot", "size": 21.619892600761666, "title": "Pertambangan Batubara dan Lignit", "x": -657, "y": -377}, {"color": "#F05D5E", "id": "_B04 - Pertambangan dan Penggalian Lainnya (O)", "label": "Pertambangan \u0026 Penggalian \nLainnya\nRp350,00 T", "physics": false, "shape": "dot", "size": 12.188961696298634, "title": "Pertambangan dan Penggalian Lainnya", "x": -739, "y": -165}, {"color": "#00A39D", "id": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "label": "Industri Kertas, Percetakan \n\u0026 Media Rekaman\nRp408,57 T", "physics": false, "shape": "dot", "size": 12.555281797048966, "title": "Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "x": -348, "y": -301}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\nRp1089,76 T", "physics": false, "shape": "dot", "size": 16.81551483178048, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": -473, "y": -571}, {"color": "#F8AD3C", "id": "_C10 - Industri Barang Galian bukan Logam (O)", "label": "Industri Barang Galian \nbukan Logam\nRp303,72 T", "physics": false, "shape": "dot", "size": 11.899531618665552, "title": "Industri Barang Galian bukan Logam", "x": -743, "y": -70}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\nRp861,78 T", "physics": false, "shape": "dot", "size": 15.38970085412097, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": -708, "y": -271}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan (O)", "label": "Ketenagalistrikan\nRp924,09 T", "physics": false, "shape": "dot", "size": 15.779412103262658, "title": "Ketenagalistrikan", "x": -608, "y": 394}, {"color": "#F8AD3C", "id": "_F - Konstruksi (O)", "label": "Konstruksi\nRp4796,82 T", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi\n\nF41016 - Konstruksi Gedung Pendidikan\nJumlah Usaha: 52", "x": -348, "y": -161}, {"color": "#F8AD3C", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "label": "Perdagangan Mobil \u0026 Motor\nRp628,54 T", "physics": false, "shape": "dot", "size": 13.930971467453958, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya", "x": -657, "y": 312}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": -515, "y": 3}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (O)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": -746, "y": 30}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (O)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": -347, "y": 311}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (O)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": -695, "y": 226}, {"color": "#00A39D", "id": "_L - Real Estate (O)", "label": "Real Estate\nRp1090,32 T", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": -728, "y": 127}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (O)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan\n\nM7431 - Aktivitas Sertifikasi Hasil Pendidikan \u0026 Pelatihan", "x": -506, "y": 216}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\nRp988,14 T", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib\n\nO84121 - Administrasi Pelayanan Pemerintah Bidang Pendidikan", "x": -539, "y": 475}, {"color": "#00A39D", "id": "_R - Jasa lainnya (O)", "label": "Jasa lainnya\nRp944,75 T", "physics": false, "shape": "dot", "size": 15.908606308830718, "title": "Jasa lainnya", "x": -443, "y": 536}, {"color": "#00A39D", "id": "_B02 - Pertambangan Batubara dan Lignit (I)", "label": "Pertambangan Batubara \n\u0026 Lignit\nRp1857,95 T", "physics": false, "shape": "dot", "size": 21.619892600761666, "title": "Pertambangan Batubara dan Lignit", "x": 621, "y": -275}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (I)", "label": "Industri Makanan \u0026 Minuman\nRp3748,51 T", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": 724, "y": -163}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan (I)", "label": "Ketenagalistrikan\nRp924,09 T", "physics": false, "shape": "dot", "size": 15.779412103262658, "title": "Ketenagalistrikan", "x": 774, "y": 98}, {"color": "#F8AD3C", "id": "_F - Konstruksi (I)", "label": "Konstruksi\nRp4796,82 T", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": 773, "y": -11}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": 645, "y": 363}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (I)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": 350, "y": -187}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara (I)", "label": "Angkutan Udara\nRp776,17 T", "physics": false, "shape": "dot", "size": 14.85425398172945, "title": "Angkutan Udara", "x": 352, "y": -75}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (I)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": 520, "y": 440}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (I)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": 341, "y": 115}, {"color": "#00A39D", "id": "_L - Real Estate (I)", "label": "Real Estate\nRp1090,32 T", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": 776, "y": 185}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (I)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": 346, "y": 242}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\nRp988,14 T", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": 741, "y": 275}, {"color": "#00A39D", "id": "_P - Jasa Pendidikan (I)", "label": "Jasa Pendidikan\nRp864,45 T", "physics": false, "shape": "dot", "size": 15.406420288740517, "title": "Jasa Pendidikan\n\nP851 - Pendidikan Anak Usia Dini dan Dasar\nJumlah Usaha: 5841\n\nP852 - Pendidikan Menengah\nJumlah Usaha: 659\n\nP853 - Pendidikan Tinggi\nJumlah Usaha: 456\n\nP854 - Pendidikan Lainnya\nJumlah Usaha: 3679\n\nP855 - Kegiatan Penunjang Pendidikan\nJumlah Usaha: 918", "x": 0, "y": 0}]);
                  edges = new vis.DataSet([{"from": "_H02 - Angkutan Darat (I)", "label": "Rp26,42 T", "to": "_B02 - Pertambangan Batubara dan Lignit (I)", "width": 0.7504287506782055}, {"from": "_H02 - Angkutan Darat (I)", "label": "Rp23,80 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 0.7224917100411209}, {"from": "_H02 - Angkutan Darat (I)", "label": "Rp207,12 T", "to": "_F - Konstruksi (I)", "width": 2.6799953607135154}, {"from": "_H02 - Angkutan Darat (I)", "label": "Rp78,50 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 1.3065882177297574}, {"from": "_H02 - Angkutan Darat (I)", "label": "Rp53,47 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 1.0392358096554533}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp22,16 T", "to": "_F - Konstruksi (I)", "width": 0.7049133373500671}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp25,00 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.7352241876989378}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp8,56 T", "to": "_H05 - Angkutan Udara (I)", "width": 0.5597182760685677}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp8,08 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.5546294001620282}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp63,08 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 1.1418679582503857}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "label": "Rp29,05 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 0.7785652728044723}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "label": "Rp23,26 T", "to": "_D01 - Ketenagalistrikan (I)", "width": 0.716663598573378}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "label": "Rp37,68 T", "to": "_F - Konstruksi (I)", "width": 0.8706814127474614}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "label": "Rp117,27 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 1.7205174313947305}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "label": "Rp21,38 T", "to": "_L - Real Estate (I)", "width": 0.6965864542012474}, {"from": "_M - Jasa Perusahaan (I)", "label": "Rp147,40 T", "to": "_F - Konstruksi (I)", "width": 2.0422292713180035}, {"from": "_M - Jasa Perusahaan (I)", "label": "Rp62,98 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 1.1408622899363423}, {"from": "_M - Jasa Perusahaan (I)", "label": "Rp72,80 T", "to": "_J - Informasi dan Komunikasi (I)", "width": 1.2457301272121724}, {"from": "_M - Jasa Perusahaan (I)", "label": "Rp55,35 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 1.0593915576719288}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp2,97 T", "to": "_H02 - Angkutan Darat (I)", "width": 0.5}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp3,27 T", "to": "_H05 - Angkutan Udara (I)", "width": 0.5032072222079662}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp4,20 T", "to": "_K01 - Jasa Perantara Keuangan (I)", "width": 0.5131701806348539}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp5,34 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.5253571445658515}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp6,18 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.5342706130166043}, {"from": "_A02 - Kehutanan dan Penebangan Kayu (O)", "label": "Rp16,87 T", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.648510504656479}, {"from": "_B02 - Pertambangan Batubara dan Lignit (O)", "label": "Rp19,54 T", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.6769900086567273}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "label": "Rp82,39 T", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 1.3480611994410199}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Rp20,67 T", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.6890722127877486}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp23,00 T", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "width": 0.7139428764424366}, {"from": "_B04 - Pertambangan dan Penggalian Lainnya (O)", "label": "Rp191,62 T", "to": "_F - Konstruksi (O)", "width": 2.5144437608650043}, {"from": "_C10 - Industri Barang Galian bukan Logam (O)", "label": "Rp245,01 T", "to": "_F - Konstruksi (O)", "width": 3.0845433513130156}, {"from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (O)", "label": "Rp154,97 T", "to": "_F - Konstruksi (O)", "width": 2.123132901714243}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp377,56 T", "to": "_F - Konstruksi (O)", "width": 4.5}, {"from": "_H02 - Angkutan Darat (O)", "label": "Rp207,12 T", "to": "_F - Konstruksi (O)", "width": 2.6799953607135154}, {"from": "_H02 - Angkutan Darat (O)", "label": "Rp78,50 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.3065882177297574}, {"from": "_J - Informasi dan Komunikasi (O)", "label": "Rp79,08 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.3127408882611615}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp117,27 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.7205174313947305}, {"from": "_L - Real Estate (O)", "label": "Rp74,07 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.2592054573653688}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp62,98 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.1408622899363423}, {"from": "_D01 - Ketenagalistrikan (O)", "label": "Rp47,92 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.9800230245785289}, {"from": "_J - Informasi dan Komunikasi (O)", "label": "Rp192,73 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 2.5263453237305096}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp72,80 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 1.2457301272121724}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (O)", "label": "Rp17,57 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.6559649503320404}, {"from": "_R - Jasa lainnya (O)", "label": "Rp32,32 T", "to": "_J - Informasi dan Komunikasi (O)", "width": 0.8134755122045081}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (O)", "label": "Rp20,59 T", "to": "_M - Jasa Perusahaan (O)", "width": 0.6882231435793258}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp15,04 T", "to": "_M - Jasa Perusahaan (O)", "width": 0.628956756906322}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp38,69 T", "to": "_M - Jasa Perusahaan (O)", "width": 0.88146119581058}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (O)", "label": "Rp30,97 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.7989932845678128}, {"from": "_F - Konstruksi (O)", "label": "Rp19,38 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.6752707260295595}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp21,45 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.6973678111918163}, {"from": "_J - Informasi dan Komunikasi (O)", "label": "Rp20,06 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.6825338867865296}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp42,66 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.9238115181727512}]);

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