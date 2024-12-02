---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-rumah-tangga
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
                                
                                    <option value="_A01a - Tanaman Pangan (I)">_A01a - Tanaman Pangan (I)</option>
                                
                                    <option value="_A01c - Tanaman Perkebunan (I)">_A01c - Tanaman Perkebunan (I)</option>
                                
                                    <option value="_A03 - Perikanan (I)">_A03 - Perikanan (I)</option>
                                
                                    <option value="_C01 - Industri Batubara dan Pengilangan Migas (I)">_C01 - Industri Batubara dan Pengilangan Migas (I)</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman (I)">_C02 - Industri Makanan dan Minuman (I)</option>
                                
                                    <option value="_C03 - Industri Pengolahan Tembakau (I)">_C03 - Industri Pengolahan Tembakau (I)</option>
                                
                                    <option value="_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (I)">_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (I)</option>
                                
                                    <option value="_D01 - Ketenagalistrikan (I)">_D01 - Ketenagalistrikan (I)</option>
                                
                                    <option value="_F - Konstruksi (I)">_F - Konstruksi (I)</option>
                                
                                    <option value="_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)">_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)</option>
                                
                                    <option value="_H02 - Angkutan Darat (I)">_H02 - Angkutan Darat (I)</option>
                                
                                    <option value="_H05 - Angkutan Udara (I)">_H05 - Angkutan Udara (I)</option>
                                
                                    <option value="_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir (I)">_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir (I)</option>
                                
                                    <option value="_I02 - Penyediaan Makan Minum (I)">_I02 - Penyediaan Makan Minum (I)</option>
                                
                                    <option value="_J - Informasi dan Komunikasi (I)">_J - Informasi dan Komunikasi (I)</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan (I)">_K01 - Jasa Perantara Keuangan (I)</option>
                                
                                    <option value="_L - Real Estate (I)">_L - Real Estate (I)</option>
                                
                                    <option value="_M - Jasa Perusahaan (I)">_M - Jasa Perusahaan (I)</option>
                                
                                    <option value="_P - Jasa Pendidikan (I)">_P - Jasa Pendidikan (I)</option>
                                
                                    <option value="_R - Jasa lainnya (I)">_R - Jasa lainnya (I)</option>
                                
                                    <option value="_X - Rumah Tangga (I)">_X - Rumah Tangga (I)</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#F8AD3C", "id": "_A01a - Tanaman Pangan (I)", "label": "Tanaman Pangan\nRp393,97 T", "physics": false, "shape": "dot", "size": 12.463924010983497, "title": "Tanaman Pangan", "x": 367, "y": -511}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan (I)", "label": "Tanaman Perkebunan\nRp937,23 T", "physics": false, "shape": "dot", "size": 15.861583718937174, "title": "Tanaman Perkebunan", "x": 163, "y": -476}, {"color": "#F05D5E", "id": "_A03 - Perikanan (I)", "label": "Perikanan\nRp569,85 T", "physics": false, "shape": "dot", "size": 13.5639165884849, "title": "Perikanan", "x": -3, "y": -394}, {"color": "#00A39D", "id": "_C01 - Industri Batubara dan Pengilangan Migas (I)", "label": "Industri Batubara \n\u0026 Pengilangan Migas\nRp750,54 T", "physics": false, "shape": "dot", "size": 14.69398043198059, "title": "Industri Batubara dan Pengilangan Migas", "x": -127, "y": -322}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman (I)", "label": "Industri Makanan \u0026 Minuman\nRp3748,51 T", "physics": false, "shape": "dot", "size": 33.44373154702451, "title": "Industri Makanan dan Minuman", "x": 321, "y": -342}, {"color": "#00A39D", "id": "_C03 - Industri Pengolahan Tembakau (I)", "label": "Industri Pengolahan Tembakau\nRp478,05 T", "physics": false, "shape": "dot", "size": 12.989781805857978, "title": "Industri Pengolahan Tembakau", "x": 69, "y": -225}, {"color": "#00A39D", "id": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (I)", "label": "Industri Kertas, Percetakan \n\u0026 Media Rekaman\nRp408,57 T", "physics": false, "shape": "dot", "size": 12.555281797048966, "title": "Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "x": -264, "y": 39}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan (I)", "label": "Ketenagalistrikan\nRp924,09 T", "physics": false, "shape": "dot", "size": 15.779412103262658, "title": "Ketenagalistrikan", "x": -170, "y": 173}, {"color": "#F8AD3C", "id": "_F - Konstruksi (I)", "label": "Konstruksi\nRp4796,82 T", "physics": false, "shape": "dot", "size": 40.0, "title": "Konstruksi", "x": -91, "y": 333}, {"color": "#F8AD3C", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "label": "Perdagangan Mobil \u0026 Motor\nRp628,54 T", "physics": false, "shape": "dot", "size": 13.930971467453958, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya", "x": -242, "y": -199}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp3321,83 T", "physics": false, "shape": "dot", "size": 30.7752201722696, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": 334, "y": 394}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat (I)", "label": "Angkutan Darat\nRp1209,78 T", "physics": false, "shape": "dot", "size": 17.566108523525358, "title": "Angkutan Darat", "x": -69, "y": -60}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara (I)", "label": "Angkutan Udara\nRp776,17 T", "physics": false, "shape": "dot", "size": 14.85425398172945, "title": "Angkutan Udara", "x": -14, "y": -153}, {"color": "#00A39D", "id": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir (I)", "label": "Pergudangan \n\u0026 Jasa Angkutan\nRp496,88 T", "physics": false, "shape": "dot", "size": 13.107545923241435, "title": "Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "x": -270, "y": -91}, {"color": "#F8AD3C", "id": "_I02 - Penyediaan Makan Minum (I)", "label": "Penyediaan Makan Minum\nRp1176,87 T", "physics": false, "shape": "dot", "size": 17.36028160789432, "title": "Penyediaan Makan Minum", "x": 170, "y": -292}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi (I)", "label": "Informasi \u0026 Komunikasi\nRp1517,46 T", "physics": false, "shape": "dot", "size": 19.49042342448277, "title": "Informasi dan Komunikasi", "x": -37, "y": 42}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan (I)", "label": "Jasa Perantara Keuangan\nRp772,69 T", "physics": false, "shape": "dot", "size": 14.832537418884648, "title": "Jasa Perantara Keuangan", "x": 160, "y": 539}, {"color": "#00A39D", "id": "_L - Real Estate (I)", "label": "Real Estate\nRp1090,32 T", "physics": false, "shape": "dot", "size": 16.819016669475765, "title": "Real Estate", "x": 44, "y": 153}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan (I)", "label": "Jasa Perusahaan\nRp1091,25 T", "physics": false, "shape": "dot", "size": 16.82482493742063, "title": "Jasa Perusahaan", "x": 58, "y": 455}, {"color": "#00A39D", "id": "_P - Jasa Pendidikan (I)", "label": "Jasa Pendidikan\nRp864,45 T", "physics": false, "shape": "dot", "size": 15.406420288740517, "title": "Jasa Pendidikan", "x": 202, "y": 340}, {"color": "#00A39D", "id": "_R - Jasa lainnya (I)", "label": "Jasa lainnya\nRp944,75 T", "physics": false, "shape": "dot", "size": 15.908606308830718, "title": "Jasa lainnya", "x": 143, "y": 266}, {"color": "#00A39D", "id": "_X - Rumah Tangga (I)", "label": "Rumah Tangga\nRp10931,24 T", "physics": false, "shape": "dot", "size": 78.36554134063385, "title": "Rumah Tangga", "x": 437, "y": -30}]);
                  edges = new vis.DataSet([{"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_X - Rumah Tangga (I)", "width": 4.5}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "to": "_X - Rumah Tangga (I)", "width": 3.184478549857236}, {"from": "_I02 - Penyediaan Makan Minum (I)", "to": "_X - Rumah Tangga (I)", "width": 2.6419222632874595}, {"from": "_L - Real Estate (I)", "to": "_X - Rumah Tangga (I)", "width": 2.3010487568125124}, {"from": "_J - Informasi dan Komunikasi (I)", "to": "_X - Rumah Tangga (I)", "width": 1.751083321345506}, {"from": "_H05 - Angkutan Udara (I)", "to": "_X - Rumah Tangga (I)", "width": 1.5948055058491237}, {"from": "_R - Jasa lainnya (I)", "to": "_X - Rumah Tangga (I)", "width": 1.5787801057823567}, {"from": "_H02 - Angkutan Darat (I)", "to": "_X - Rumah Tangga (I)", "width": 1.532463639178862}, {"from": "_C03 - Industri Pengolahan Tembakau (I)", "to": "_X - Rumah Tangga (I)", "width": 1.4222594553456314}, {"from": "_P - Jasa Pendidikan (I)", "to": "_X - Rumah Tangga (I)", "width": 1.401723870394091}, {"from": "_A01a - Tanaman Pangan (I)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 1.1435466347259868}, {"from": "_A01c - Tanaman Perkebunan (I)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 1.5878180591611022}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_C02 - Industri Makanan dan Minuman (I)", "width": 1.6785033697579408}, {"from": "_A01c - Tanaman Perkebunan (I)", "to": "_C03 - Industri Pengolahan Tembakau (I)", "width": 0.5764521908456164}, {"from": "_C03 - Industri Pengolahan Tembakau (I)", "to": "_C03 - Industri Pengolahan Tembakau (I)", "width": 0.5506509026993905}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (I)", "to": "_C03 - Industri Pengolahan Tembakau (I)", "width": 0.502995928664118}, {"from": "_H02 - Angkutan Darat (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6568315572053548}, {"from": "_J - Informasi dan Komunikasi (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.6581469506647475}, {"from": "_K01 - Jasa Perantara Keuangan (I)", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "width": 0.7453264274454161}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas (I)", "to": "_H02 - Angkutan Darat (I)", "width": 0.708050466082819}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya (I)", "to": "_H02 - Angkutan Darat (I)", "width": 0.6312145230088501}, {"from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir (I)", "to": "_H02 - Angkutan Darat (I)", "width": 0.5744389902099467}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.6931078301674815}, {"from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.6175181614840651}, {"from": "_J - Informasi dan Komunikasi (I)", "to": "_H05 - Angkutan Udara (I)", "width": 0.5760158757792553}, {"from": "_A03 - Perikanan (I)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 0.6393502852498475}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 1.3295480307308316}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "to": "_I02 - Penyediaan Makan Minum (I)", "width": 0.698167180368338}, {"from": "_D01 - Ketenagalistrikan (I)", "to": "_J - Informasi dan Komunikasi (I)", "width": 0.5870144400781033}, {"from": "_J - Informasi dan Komunikasi (I)", "to": "_J - Informasi dan Komunikasi (I)", "width": 0.9176062096252628}, {"from": "_M - Jasa Perusahaan (I)", "to": "_J - Informasi dan Komunikasi (I)", "width": 0.6438205672930298}, {"from": "_F - Konstruksi (I)", "to": "_L - Real Estate (I)", "width": 0.7038248570378753}, {"from": "_J - Informasi dan Komunikasi (I)", "to": "_L - Real Estate (I)", "width": 0.5787364042856902}, {"from": "_M - Jasa Perusahaan (I)", "to": "_L - Real Estate (I)", "width": 0.5632293221649081}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.54831167926063}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.5265849376280071}, {"from": "_M - Jasa Perusahaan (I)", "to": "_P - Jasa Pendidikan (I)", "width": 0.5749968539760413}, {"from": "_C02 - Industri Makanan dan Minuman (I)", "to": "_R - Jasa lainnya (I)", "width": 0.5457233009357428}, {"from": "_L - Real Estate (I)", "to": "_R - Jasa lainnya (I)", "width": 0.5461457045304916}, {"from": "_M - Jasa Perusahaan (I)", "to": "_R - Jasa lainnya (I)", "width": 0.5312006162277941}]);

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