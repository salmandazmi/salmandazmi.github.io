---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-industri-mamin-value
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
                                
                                    <option value="_A03 - Perikanan (O)">_A03 - Perikanan (O)</option>
                                
                                    <option value="_C01 - Industri Batubara dan Pengilangan Migas (O)">_C01 - Industri Batubara dan Pengilangan Migas (O)</option>
                                
                                    <option value="_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)">_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)</option>
                                
                                    <option value="_H02 - Angkutan Darat (O)">_H02 - Angkutan Darat (O)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (O)">_J - Informasi dan Komunikasi (O)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (O)">_K01 - Jasa Perantara Keuangan (O)</option>
                                
                                    <option value="_L - Real Estate (O)">_L - Real Estate (O)</option>
                                
                                    <option value="_M - Jasa Perusahaan (O)">_M - Jasa Perusahaan (O)</option>
                                
                                    <option value="_A01c - Tanaman Perkebunan (I)">_A01c - Tanaman Perkebunan (I)</option>
                                
                                    <option value="_A01d - Peternakan (I)">_A01d - Peternakan (I)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (I)">_C02 - Industri Makanan dan Minuman (I)</option>
                                
                                    <option value="_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)">_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)</option>
                                
                                    <option value="_C09 - Industri Karet, Barang dari Karet dan Plastik (I)">_C09 - Industri Karet, Barang dari Karet dan Plastik (I)</option>
                                
                                    <option value="_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)">_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#F8AD3C", "id": "_A01a - Tanaman Pangan (O)", "label": "Tanaman Pangan\nRp393,97 T", "physics": false, "shape": "dot", "size": 12.463924010983497, "title": "Tanaman Pangan", "x": -266, "y": -49}, {"color": "#F8AD3C", "id": "_A01b - Tanaman Hortikultura (O)", "label": "Tanaman Hortikultura\nRp388,53 T", "physics": false, "shape": "dot", "size": 12.429930618730301, "title": "Tanaman Hortikultura", "x": -586, "y": -68}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan (O)", "label": "Tanaman Perkebunan\nRp937,23 T", "physics": false, "shape": "dot", "size": 15.861583718937174, "title": "Tanaman Perkebunan", "x": -303, "y": -226}, {"color": "#F8AD3C", "id": "_A01d - Peternakan (O)", "label": "Peternakan\nRp475,00 T", "physics": false, "shape": "dot", "size": 12.970730185984145, "title": "Peternakan", "x": -199, "y": 181}, {"color": "#00A39D", "id": "_A01e - Jasa Pertanian dan Perburuan (O)", "label": "Jasa Pertanian \u0026 Perburuan\nRp37,34 T", "physics": false, "shape": "dot", "size": 10.233540079335247, "title": "Jasa Pertanian dan Perburuan", "x": -509, "y": -293}, {"color": "#F05D5E", "id": "_A03 - Perikanan (O)", "label": "Perikanan\nRp569,85 T", "physics": false, "shape": "dot", "size": 13.5639165884849, "title": "Perikanan", "x": -333, "y": 397}, {"color": "#00A39D", "id": "_C01 - Industri Batubara dan Pengilangan Migas (O)", "label": "Industri Batubara \n\u0026 Pengilangan Migas\nRp750,54 T", "physics": false, "shape": "dot", "size": 14.69398043198059, "title": "Industri Batubara dan Pengilangan Migas", "x": -445, "y": 553}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\nRp1089,76 T", "physics": false, "shape": "dot", "size": 16.81551483178048, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": -686, "y": -185}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": -360, "y": 213}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (O)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": -600, "y": 269}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (O)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": -496, "y": 468}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (O)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": -655, "y": 190}, {"color": "#00A39D", "id": "_L - Real Estate (O)", "label": "Real Estate\nRp1090,32 T", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": -527, "y": 380}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (O)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": -633, "y": 378}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan (I)", "label": "Tanaman Perkebunan\nRp937,23 T", "physics": false, "shape": "dot", "size": 15.861583718937174, "title": "Tanaman Perkebunan", "x": 382, "y": -449}, {"color": "#F8AD3C", "id": "_A01d - Peternakan (I)", "label": "Peternakan\nRp475,00 T", "physics": false, "shape": "dot", "size": 12.970730185984145, "title": "Peternakan", "x": 365, "y": -148}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (I)", "label": "Industri Makanan \u0026 Minuman\nRp3748,51 T", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": 0, "y": 0}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\nRp1089,76 T", "physics": false, "shape": "dot", "size": 16.81551483178048, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": 340, "y": -268}, {"color": "#00A39D", "id": "_C09 - Industri Karet, Barang dari Karet dan Plastik (I)", "label": "Industri Karet \u0026 Plastik\nRp291,42 T", "physics": false, "shape": "dot", "size": 11.822602799809339, "title": "Industri Karet, Barang dari Karet dan Plastik", "x": 489, "y": -471}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\nRp861,78 T", "physics": false, "shape": "dot", "size": 15.38970085412097, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": 519, "y": -390}, {"color": "#F8AD3C", "id": "_F - Konstruksi (I)", "label": "Konstruksi\nRp4796,82 T", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": 522, "y": 244}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": 552, "y": -8}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (I)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": 586, "y": 551}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara (I)", "label": "Angkutan Udara\nRp776,17 T", "physics": false, "shape": "dot", "size": 14.85425398172945, "title": "Angkutan Udara", "x": 303, "y": 145}, {"color": "#F8AD3C", "id": "_I01 - Penyediaan Akomodasi (I)", "label": "Penyediaan Akomodasi\nRp212,01 T", "physics": false, "shape": "dot", "size": 11.32591423032543, "title": "Penyediaan Akomodasi", "x": 363, "y": 4}, {"color": "#F8AD3C", "id": "_I02 - Penyediaan Makan Minum (I)", "label": "Penyediaan Makan Minum\nRp1176,87 T", "physics": false, "shape": "dot", "size": 17.36028160789432, "title": "Penyediaan Makan Minum", "x": 8, "y": 216}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (I)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": 489, "y": 623}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (I)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": 636, "y": 466}, {"color": "#00A39D", "id": "_L - Real Estate (I)", "label": "Real Estate\nRp1090,32 T", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": 699, "y": 357}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (I)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": 734, "y": 232}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\nRp988,14 T", "physics": false, "shape": "dot", "size": 16.1799628979183, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": 391, "y": 481}, {"color": "#00A39D", "id": "_P - Jasa Pendidikan (I)", "label": "Jasa Pendidikan\nRp864,45 T", "physics": false, "shape": "dot", "size": 15.406420288740517, "title": "Jasa Pendidikan", "x": 403, "y": 358}, {"color": "#00A39D", "id": "_R - Jasa lainnya (I)", "label": "Jasa lainnya\nRp944,75 T", "physics": false, "shape": "dot", "size": 15.908606308830718, "title": "Jasa lainnya", "x": 614, "y": -161}]);
                  edges = new vis.DataSet([{"from": "_A01d - Peternakan (I)", "label": "Rp3,57 T", "to": "_A01d - Peternakan (I)", "width": 0.5137053604177713}, {"from": "_A01d - Peternakan (I)", "label": "Rp4,87 T", "to": "_I01 - Penyediaan Akomodasi (I)", "width": 0.52359442184878}, {"from": "_A01d - Peternakan (I)", "label": "Rp4,99 T", "to": "_R - Jasa lainnya (I)", "width": 0.5245391231892644}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "label": "Rp119,77 T", "to": "_A01d - Peternakan (I)", "width": 1.4002388832707653}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "label": "Rp526,04 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 4.5}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "label": "Rp74,48 T", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "width": 1.0547484279835253}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "label": "Rp40,69 T", "to": "_I01 - Penyediaan Akomodasi (I)", "width": 0.796879799386086}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "label": "Rp373,18 T", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 3.3337384873048705}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Rp34,17 T", "to": "_A01c - Tanaman Perkebunan (I)", "width": 0.7471748583014416}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Rp92,56 T", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "width": 1.1926562061939747}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Rp92,66 T", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik (I)", "width": 1.1934475607606052}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Rp37,71 T", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "width": 0.7741519109987682}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (I)", "label": "Rp31,14 T", "to": "_F - Konstruksi (I)", "width": 0.7240415298316635}, {"from": "_I01 - Penyediaan Akomodasi (I)", "label": "Rp3,99 T", "to": "_H05 - Angkutan Udara (I)", "width": 0.516929450973239}, {"from": "_I01 - Penyediaan Akomodasi (I)", "label": "Rp3,51 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.5132431566168264}, {"from": "_I01 - Penyediaan Akomodasi (I)", "label": "Rp14,90 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.6001534379095229}, {"from": "_I01 - Penyediaan Akomodasi (I)", "label": "Rp11,38 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.5732655515616215}, {"from": "_I01 - Penyediaan Akomodasi (I)", "label": "Rp10,51 T", "to": "_R - Jasa lainnya (I)", "width": 0.5666164281372141}, {"from": "_I02 - Penyediaan Makan Minum (I)", "label": "Rp27,27 T", "to": "_F - Konstruksi (I)", "width": 0.6945552318755811}, {"from": "_I02 - Penyediaan Makan Minum (I)", "label": "Rp23,09 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6625990486840323}, {"from": "_I02 - Penyediaan Makan Minum (I)", "label": "Rp13,87 T", "to": "_H05 - Angkutan Udara (I)", "width": 0.5922870087643385}, {"from": "_I02 - Penyediaan Makan Minum (I)", "label": "Rp43,81 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.8207141010689653}, {"from": "_I02 - Penyediaan Makan Minum (I)", "label": "Rp13,67 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.5907374627513217}, {"from": "_A01a - Tanaman Pangan (O)", "label": "Rp21,43 T", "to": "_A01a - Tanaman Pangan (O)", "width": 0.649989324716319}, {"from": "_A01e - Jasa Pertanian dan Perburuan (O)", "label": "Rp7,84 T", "to": "_A01a - Tanaman Pangan (O)", "width": 0.5462743316942176}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Rp21,77 T", "to": "_A01a - Tanaman Pangan (O)", "width": 0.6525397416606734}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp11,36 T", "to": "_A01a - Tanaman Pangan (O)", "width": 0.5731496817573244}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp8,45 T", "to": "_A01a - Tanaman Pangan (O)", "width": 0.5509021556365126}, {"from": "_A01c - Tanaman Perkebunan (O)", "label": "Rp35,14 T", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.7545350777184326}, {"from": "_A01e - Jasa Pertanian dan Perburuan (O)", "label": "Rp23,77 T", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.6677899575666557}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Rp34,17 T", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.7471748583014416}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp15,65 T", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.6058261129649384}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp17,59 T", "to": "_A01c - Tanaman Perkebunan (O)", "width": 0.6206657076426159}, {"from": "_A01a - Tanaman Pangan (O)", "label": "Rp1,78 T", "to": "_A01d - Peternakan (O)", "width": 0.5}, {"from": "_A01d - Peternakan (O)", "label": "Rp3,57 T", "to": "_A01d - Peternakan (O)", "width": 0.5137053604177713}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp24,48 T", "to": "_A01d - Peternakan (O)", "width": 0.6732360441059627}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp5,77 T", "to": "_A01d - Peternakan (O)", "width": 0.5304951637519888}, {"from": "_A01a - Tanaman Pangan (O)", "label": "Rp291,70 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 2.7120936943843525}, {"from": "_A01c - Tanaman Perkebunan (O)", "label": "Rp486,31 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 4.196915978386333}, {"from": "_A01d - Peternakan (O)", "label": "Rp204,72 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 2.04840686248141}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp162,44 T", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 1.7258516650023086}, {"from": "_H02 - Angkutan Darat (O)", "label": "Rp78,50 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0854183033634928}, {"from": "_J - Informasi dan Komunikasi (O)", "label": "Rp79,08 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0898145472209948}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp117,27 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.3811815420624}, {"from": "_L - Real Estate (O)", "label": "Rp74,07 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 1.0515620830214112}, {"from": "_M - Jasa Perusahaan (O)", "label": "Rp62,98 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "width": 0.967002797710794}, {"from": "_F - Konstruksi (I)", "label": "Rp75,38 T", "to": "_F - Konstruksi (I)", "width": 1.0616247596655155}, {"from": "_F - Konstruksi (I)", "label": "Rp25,40 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6802703173892923}, {"from": "_F - Konstruksi (I)", "label": "Rp99,09 T", "to": "_L - Real Estate (I)", "width": 1.2424770178167863}, {"from": "_F - Konstruksi (I)", "label": "Rp65,08 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.9829672873531171}, {"from": "_F - Konstruksi (I)", "label": "Rp19,38 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.6343254598812026}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Rp71,67 T", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik (I)", "width": 1.033250393879516}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Rp45,15 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.8309612096213734}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp22,16 T", "to": "_F - Konstruksi (I)", "width": 0.6555058801999826}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp25,00 T", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6771637747649114}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp8,56 T", "to": "_H05 - Angkutan Udara (I)", "width": 0.5517602151200103}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp8,08 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.5481240803178091}, {"from": "_H05 - Angkutan Udara (I)", "label": "Rp63,08 T", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "width": 0.9677213739982339}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Rp4,64 T", "to": "_H02 - Angkutan Darat (I)", "width": 0.5218291422969922}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Rp17,57 T", "to": "_J - Informasi dan Komunikasi (I)", "width": 0.6205309787056978}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Rp9,80 T", "to": "_L - Real Estate (I)", "width": 0.5612635228274067}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib (I)", "label": "Rp6,41 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.5353549667044627}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp2,97 T", "to": "_H02 - Angkutan Darat (I)", "width": 0.509089946493233}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp3,27 T", "to": "_H05 - Angkutan Udara (I)", "width": 0.5113815905621701}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp4,20 T", "to": "_K01 - Jasa Perantara Keuangan (I)", "width": 0.5185003846798133}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp5,34 T", "to": "_M - Jasa Perusahaan (I)", "width": 0.5272082888410693}, {"from": "_P - Jasa Pendidikan (I)", "label": "Rp6,18 T", "to": "_P - Jasa Pendidikan (I)", "width": 0.5335771949486942}, {"from": "_A01b - Tanaman Hortikultura (O)", "label": "Rp9,14 T", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5561531095620559}, {"from": "_A01e - Jasa Pertanian dan Perburuan (O)", "label": "Rp4,00 T", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5169858930023474}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional (O)", "label": "Rp17,10 T", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.6169138482903446}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp10,67 T", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5678911989174869}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp5,56 T", "to": "_A01b - Tanaman Hortikultura (O)", "width": 0.5288498327589529}, {"from": "_A03 - Perikanan (O)", "label": "Rp13,52 T", "to": "_A03 - Perikanan (O)", "width": 0.589593168389115}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas (O)", "label": "Rp6,04 T", "to": "_A03 - Perikanan (O)", "width": 0.5325120111202104}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp8,87 T", "to": "_A03 - Perikanan (O)", "width": 0.5540944202173345}, {"from": "_K01 - Jasa Perantara Keuangan (O)", "label": "Rp8,96 T", "to": "_A03 - Perikanan (O)", "width": 0.5548322227395414}, {"from": "_A01b - Tanaman Hortikultura (O)", "label": "Rp43,77 T", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 0.8204249861337758}, {"from": "_A01d - Peternakan (O)", "label": "Rp32,50 T", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 0.7344016229141944}, {"from": "_A03 - Perikanan (O)", "label": "Rp70,85 T", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 1.0269932489381723}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (O)", "label": "Rp96,61 T", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 1.223568206822948}]);

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