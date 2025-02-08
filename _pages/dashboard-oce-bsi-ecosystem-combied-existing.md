---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-combined-existing
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
                                
                                    <option value="_A01a - Tanaman Pangan">_A01a - Tanaman Pangan</option>
                                
                                    <option value="_A01b - Tanaman Hortikultura">_A01b - Tanaman Hortikultura</option>
                                
                                    <option value="_A01c - Tanaman Perkebunan">_A01c - Tanaman Perkebunan</option>
                                
                                    <option value="_A01d - Peternakan">_A01d - Peternakan</option>
                                
                                    <option value="_A01e - Jasa Pertanian dan Perburuan">_A01e - Jasa Pertanian dan Perburuan</option>
                                
                                    <option value="_A02 - Kehutanan dan Penebangan Kayu">_A02 - Kehutanan dan Penebangan Kayu</option>
                                
                                    <option value="_A03 - Perikanan">_A03 - Perikanan</option>
                                
                                    <option value="_B01 - Pertambangan Minyak, Gas dan Panas Bumi">_B01 - Pertambangan Minyak, Gas dan Panas Bumi</option>
                                
                                    <option value="_B02 - Pertambangan Batubara dan Lignit">_B02 - Pertambangan Batubara dan Lignit</option>
                                
                                    <option value="_B03 - Pertambangan Bijih Logam">_B03 - Pertambangan Bijih Logam</option>
                                
                                    <option value="_B04 - Pertambangan dan Penggalian Lainnya">_B04 - Pertambangan dan Penggalian Lainnya</option>
                                
                                    <option value="_C01 - Industri Batubara dan Pengilangan Migas">_C01 - Industri Batubara dan Pengilangan Migas</option>
                                
                                    <option value="_C02 - Industri Makanan dan Minuman">_C02 - Industri Makanan dan Minuman</option>
                                
                                    <option value="_C03 - Industri Pengolahan Tembakau">_C03 - Industri Pengolahan Tembakau</option>
                                
                                    <option value="_C04 - Industri Tekstil dan Pakaian Jadi">_C04 - Industri Tekstil dan Pakaian Jadi</option>
                                
                                    <option value="_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki">_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki</option>
                                
                                    <option value="_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya">_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya</option>
                                
                                    <option value="_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman">_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman</option>
                                
                                    <option value="_C08 - Industri Kimia, Farmasi dan Obat Tradisional">_C08 - Industri Kimia, Farmasi dan Obat Tradisional</option>
                                
                                    <option value="_C09 - Industri Karet, Barang dari Karet dan Plastik">_C09 - Industri Karet, Barang dari Karet dan Plastik</option>
                                
                                    <option value="_C10 - Industri Barang Galian bukan Logam">_C10 - Industri Barang Galian bukan Logam</option>
                                
                                    <option value="_C11 - Industri Logam Dasar">_C11 - Industri Logam Dasar</option>
                                
                                    <option value="_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik">_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik</option>
                                
                                    <option value="_C13 - Industri Mesin dan Perlengkapan">_C13 - Industri Mesin dan Perlengkapan</option>
                                
                                    <option value="_C14 - Industri Alat Angkutan">_C14 - Industri Alat Angkutan</option>
                                
                                    <option value="_C15 - Industri Furnitur">_C15 - Industri Furnitur</option>
                                
                                    <option value="_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan">_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan</option>
                                
                                    <option value="_D01 - Ketenagalistrikan">_D01 - Ketenagalistrikan</option>
                                
                                    <option value="_D02 - Pengadaan Gas dan Produksi Es">_D02 - Pengadaan Gas dan Produksi Es</option>
                                
                                    <option value="_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang">_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang</option>
                                
                                    <option value="_F - Konstruksi">_F - Konstruksi</option>
                                
                                    <option value="_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya">_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya</option>
                                
                                    <option value="_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor">_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor</option>
                                
                                    <option value="_H01 - Angkutan Rel">_H01 - Angkutan Rel</option>
                                
                                    <option value="_H02 - Angkutan Darat">_H02 - Angkutan Darat</option>
                                
                                    <option value="_H03 - Angkutan Laut">_H03 - Angkutan Laut</option>
                                
                                    <option value="_H04 - Angkutan Sungai Danau dan Penyeberangan">_H04 - Angkutan Sungai Danau dan Penyeberangan</option>
                                
                                    <option value="_H05 - Angkutan Udara">_H05 - Angkutan Udara</option>
                                
                                    <option value="_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir">_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir</option>
                                
                                    <option value="_I01 - Penyediaan Akomodasi">_I01 - Penyediaan Akomodasi</option>
                                
                                    <option value="_I02 - Penyediaan Makan Minum">_I02 - Penyediaan Makan Minum</option>
                                
                                    <option value="_J - Informasi dan Komunikasi">_J - Informasi dan Komunikasi</option>
                                
                                    <option value="_K01 - Jasa Perantara Keuangan">_K01 - Jasa Perantara Keuangan</option>
                                
                                    <option value="_K02 - Asuransi dan Dana Pensiun">_K02 - Asuransi dan Dana Pensiun</option>
                                
                                    <option value="_K04 - Jasa Penunjang Keuangan">_K04 - Jasa Penunjang Keuangan</option>
                                
                                    <option value="_L - Real Estate">_L - Real Estate</option>
                                
                                    <option value="_M - Jasa Perusahaan">_M - Jasa Perusahaan</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib</option>
                                
                                    <option value="_P - Jasa Pendidikan">_P - Jasa Pendidikan</option>
                                
                                    <option value="_Q - Jasa Kesehatan dan Kegiatan Sosial">_Q - Jasa Kesehatan dan Kegiatan Sosial</option>
                                
                                    <option value="_R - Jasa lainnya">_R - Jasa lainnya</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#F8AD3C", "id": "_A01a - Tanaman Pangan", "label": "Tanaman Pangan\nRp471,64 T", "physics": false, "shape": "dot", "size": 13.627925402211554, "title": "Tanaman Pangan", "x": 500.0, "y": 3.8348576090600624e-06}, {"color": "#F8AD3C", "id": "_A01b - Tanaman Hortikultura", "label": "Tanaman Hortikultura\nRp286,67 T", "physics": false, "shape": "dot", "size": 12.205152636035017, "title": "Tanaman Hortikultura", "x": 496.21024732873354, "y": -61.44414004204645}, {"color": "#00A39D", "id": "_A01c - Tanaman Perkebunan", "label": "Tanaman Perkebunan\nRp811,30 T", "physics": false, "shape": "dot", "size": 16.24068386605136, "title": "Tanaman Perkebunan", "x": 484.8984779940537, "y": -121.95684959804275}, {"color": "#F8AD3C", "id": "_A01d - Peternakan", "label": "Peternakan\nRp325,45 T", "physics": false, "shape": "dot", "size": 12.503437124845707, "title": "Peternakan", "x": 466.2361149520492, "y": -180.6208279887177}, {"color": "#00A39D", "id": "_A01e - Jasa Pertanian dan Perburuan", "label": "Jasa Pertanian \u0026 Perburuan\nRp37,45 T", "physics": false, "shape": "dot", "size": 10.288043882379306, "title": "Jasa Pertanian dan Perburuan", "x": 440.5061014476971, "y": -236.5467627500068}, {"color": "#00A39D", "id": "_A02 - Kehutanan dan Penebangan Kayu", "label": "Kehutanan \u0026 Penebangan \nKayu\nRp130,12 T", "physics": false, "shape": "dot", "size": 11.00088024241666, "title": "Kehutanan dan Penebangan Kayu", "x": 408.0984604688859, "y": -288.88692683289275}, {"color": "#F05D5E", "id": "_A03 - Perikanan", "label": "Perikanan\nRp555,04 T", "physics": false, "shape": "dot", "size": 14.269479818525962, "title": "Perikanan", "x": 369.5044534913054, "y": -336.84780361205895}, {"color": "#00A39D", "id": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "label": "Pertambangan Minyak, Gas \n\u0026 Panas Bumi\nRp521,07 T", "physics": false, "shape": "dot", "size": 14.008167049652032, "title": "Pertambangan Minyak, Gas dan Panas Bumi", "x": 325.30915970027144, "y": -379.70243994835437}, {"color": "#00A39D", "id": "_B02 - Pertambangan Batubara dan Lignit", "label": "Pertambangan Batubara \n\u0026 Lignit\nRp1.116,57 T", "physics": false, "shape": "dot", "size": 18.588871151279136, "title": "Pertambangan Batubara dan Lignit", "x": 276.18250549180635, "y": -416.80117502471}, {"color": "#00A39D", "id": "_B03 - Pertambangan Bijih Logam", "label": "Pertambangan Bijih Logam\nRp272,02 T", "physics": false, "shape": "dot", "size": 12.092443658587992, "title": "Pertambangan Bijih Logam", "x": 222.86917638664556, "y": -447.5816390251669}, {"color": "#F05D5E", "id": "_B04 - Pertambangan dan Penggalian Lainnya", "label": "Pertambangan \u0026 Penggalian \nLainnya\nRp288,36 T", "physics": false, "shape": "dot", "size": 12.21808473814006, "title": "Pertambangan dan Penggalian Lainnya", "x": 166.17741135716895, "y": -471.57721699432113}, {"color": "#00A39D", "id": "_C01 - Industri Batubara dan Pengilangan Migas", "label": "Industri Batubara \n\u0026 Pengilangan Migas\nRp400,45 T", "physics": false, "shape": "dot", "size": 13.0803102617343, "title": "Industri Batubara dan Pengilangan Migas", "x": 106.96654545680899, "y": -488.4241417899578}, {"color": "#00A39D", "id": "_C02 - Industri Makanan dan Minuman", "label": "Industri Makanan \u0026 Minuman\nRp1.368,43 T", "physics": false, "shape": "dot", "size": 20.526201891149423, "title": "Industri Makanan dan Minuman", "x": 46.13419109630454, "y": -497.86706711726305}, {"color": "#00A39D", "id": "_C03 - Industri Pengolahan Tembakau", "label": "Industri Pengolahan Tembakau\nRp147,83 T", "physics": false, "shape": "dot", "size": 11.137105769087773, "title": "Industri Pengolahan Tembakau", "x": -15.397512933929901, "y": -499.762852423717}, {"color": "#F05D5E", "id": "_C04 - Industri Tekstil dan Pakaian Jadi", "label": "Industri Tekstil \u0026 Pakaian Jadi\nRp203,95 T", "physics": false, "shape": "dot", "size": 11.568818905955855, "title": "Industri Tekstil dan Pakaian Jadi", "x": -76.69580704098058, "y": -494.08273846859885}, {"color": "#00A39D", "id": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "label": "Industri Kulit, Barang dari Kulit \n\u0026 Alas Kaki\nRp49,24 T", "physics": false, "shape": "dot", "size": 10.378770901590794, "title": "Industri Kulit, Barang dari Kulit dan Alas Kaki", "x": -136.83145389386397, "y": -480.91282416013894}, {"color": "#00A39D", "id": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "label": "Industri Kayu, Gabus \n\u0026 Anyaman Bambu\nRp81,98 T", "physics": false, "shape": "dot", "size": 10.630612282573969, "title": "Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "x": -194.89290383969782, "y": -460.452755253351}, {"color": "#00A39D", "id": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "label": "Industri Kertas, Percetakan \n\u0026 Media Rekaman\nRp141,01 T", "physics": false, "shape": "dot", "size": 11.084670448828762, "title": "Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "x": -250.0000198043004, "y": -433.0126845131892}, {"color": "#F8AD3C", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional\nRp366,32 T", "physics": false, "shape": "dot", "size": 12.817799819592611, "title": "Industri Kimia, Farmasi dan Obat Tradisional", "x": -301.3173234929925, "y": -399.00859274999567}, {"color": "#00A39D", "id": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "label": "Industri Karet \u0026 Plastik\nRp83,86 T", "physics": false, "shape": "dot", "size": 10.645098975349622, "title": "Industri Karet, Barang dari Karet dan Plastik", "x": -348.06697430182453, "y": -358.9559409249166}, {"color": "#F8AD3C", "id": "_C10 - Industri Barang Galian bukan Logam", "label": "Industri Barang Galian \nbukan Logam\nRp98,39 T", "physics": false, "shape": "dot", "size": 10.75680489181967, "title": "Industri Barang Galian bukan Logam", "x": -389.5402730138644, "y": -313.46189174386257}, {"color": "#00A39D", "id": "_C11 - Industri Logam Dasar", "label": "Industri Logam Dasar\nRp196,32 T", "physics": false, "shape": "dot", "size": 11.510092916471585, "title": "Industri Logam Dasar", "x": -425.1085396467246, "y": -263.2160709376916}, {"color": "#00A39D", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "label": "Industri Barang Logam, \nElektronik \u0026 Optik\nRp327,99 T", "physics": false, "shape": "dot", "size": 12.522936814850636, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "x": -454.2326203932765, "y": -208.9801662518347}, {"color": "#00A39D", "id": "_C13 - Industri Mesin dan Perlengkapan", "label": "Industri Mesin \n\u0026 Perlengkapan\nRp56,49 T", "physics": false, "shape": "dot", "size": 10.434524630634078, "title": "Industri Mesin dan Perlengkapan", "x": -476.4709938532322, "y": -151.57633434304122}, {"color": "#F8AD3C", "id": "_C14 - Industri Alat Angkutan", "label": "Industri Alat Angkutan\nRp310,89 T", "physics": false, "shape": "dot", "size": 12.391442751422925, "title": "Industri Alat Angkutan", "x": -491.4865361602362, "y": -91.87475830994815}, {"color": "#F8AD3C", "id": "_C15 - Industri Furnitur", "label": "Industri Furnitur\nRp42,18 T", "physics": false, "shape": "dot", "size": 10.324488687615736, "title": "Industri Furnitur", "x": -499.0516469812477, "y": -30.78046202824438}, {"color": "#00A39D", "id": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "label": "Industri Pengolahan Lainnya\nRp24,74 T", "physics": false, "shape": "dot", "size": 10.190314666792476, "title": "Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "x": -499.0516469812477, "y": 30.780438032992482}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan", "label": "Ketenagalistrikan\nRp184,46 T", "physics": false, "shape": "dot", "size": 11.418900520043014, "title": "Ketenagalistrikan", "x": -491.4865361602362, "y": 91.87473617734136}, {"color": "#00A39D", "id": "_D02 - Pengadaan Gas dan Produksi Es", "label": "Pengadaan Gas \n\u0026 Produksi Es\nRp33,79 T", "physics": false, "shape": "dot", "size": 10.259925867845178, "title": "Pengadaan Gas dan Produksi Es", "x": -476.4709938532322, "y": 151.57631221043442}, {"color": "#00A39D", "id": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "label": "Pengadaan Air \n\u0026 Pengelolaan Limbah\nRp13,29 T", "physics": false, "shape": "dot", "size": 10.102192990778095, "title": "Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "x": -454.23265019559847, "y": 208.9801441192279}, {"color": "#F8AD3C", "id": "_F - Konstruksi", "label": "Konstruksi\nRp2.072,39 T", "physics": false, "shape": "dot", "size": 25.94116965320039, "title": "Konstruksi", "x": -425.10856944904657, "y": 263.2160488050848}, {"color": "#F8AD3C", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "label": "Perdagangan Mobil \u0026 Motor\nRp467,18 T", "physics": false, "shape": "dot", "size": 13.593672869815915, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya", "x": -389.5403028161864, "y": 313.46186961125574}, {"color": "#F8AD3C", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor\nRp2.235,26 T", "physics": false, "shape": "dot", "size": 27.194042043434184, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "x": -348.0670041041465, "y": 358.9559187923098}, {"color": "#00A39D", "id": "_H01 - Angkutan Rel", "label": "Angkutan Rel\nRp18,76 T", "physics": false, "shape": "dot", "size": 10.144270014169006, "title": "Angkutan Rel", "x": -301.3173532953145, "y": 399.0085706173889}, {"color": "#00A39D", "id": "_H02 - Angkutan Darat", "label": "Angkutan Darat\nRp564,76 T", "physics": false, "shape": "dot", "size": 14.344232476304223, "title": "Angkutan Darat", "x": -249.99994529849542, "y": 433.01272198522634}, {"color": "#00A39D", "id": "_H03 - Angkutan Laut", "label": "Angkutan Laut\nRp68,73 T", "physics": false, "shape": "dot", "size": 10.5286715954772, "title": "Angkutan Laut", "x": -194.8929932466638, "y": 460.4527331207442}, {"color": "#F8AD3C", "id": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "label": "Angkutan Sungai Danau\nRp34,13 T", "physics": false, "shape": "dot", "size": 10.262514288234392, "title": "Angkutan Sungai Danau dan Penyeberangan", "x": -136.83143899270297, "y": 480.91283182985416}, {"color": "#00A39D", "id": "_H05 - Angkutan Udara", "label": "Angkutan Udara\nRp288,92 T", "physics": false, "shape": "dot", "size": 12.22241774617942, "title": "Angkutan Udara", "x": -76.69589644794655, "y": 494.0827163359921}, {"color": "#00A39D", "id": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "label": "Pergudangan \n\u0026 Jasa Angkutan\nRp255,95 T", "physics": false, "shape": "dot", "size": 11.968834854420264, "title": "Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "x": -15.397485925575596, "y": 499.7628600934322}, {"color": "#F8AD3C", "id": "_I01 - Penyediaan Akomodasi", "label": "Penyediaan Akomodasi\nRp120,46 T", "physics": false, "shape": "dot", "size": 10.926628345908346, "title": "Penyediaan Akomodasi", "x": 46.13410168933857, "y": 497.86710458930025}, {"color": "#F8AD3C", "id": "_I02 - Penyediaan Makan Minum", "label": "Penyediaan Makan Minum\nRp405,80 T", "physics": false, "shape": "dot", "size": 13.121488068651217, "title": "Penyediaan Makan Minum", "x": 106.96657525913098, "y": 488.424149459673}, {"color": "#00A39D", "id": "_J - Informasi dan Komunikasi", "label": "Informasi \u0026 Komunikasi\nRp883,64 T", "physics": false, "shape": "dot", "size": 16.797099635851943, "title": "Informasi dan Komunikasi", "x": 166.17730704904199, "y": 471.57725446635834}, {"color": "#00A39D", "id": "_K01 - Jasa Perantara Keuangan", "label": "Jasa Perantara Keuangan\nRp558,68 T", "physics": false, "shape": "dot", "size": 14.297483988716078, "title": "Jasa Perantara Keuangan", "x": 222.86919128780656, "y": 447.58164669488207}, {"color": "#00A39D", "id": "_K02 - Asuransi dan Dana Pensiun", "label": "Asuransi \u0026 Dana Pensiun\nRp173,16 T", "physics": false, "shape": "dot", "size": 11.331992670885745, "title": "Asuransi dan Dana Pensiun", "x": 276.1823862825184, "y": 416.8012422990692}, {"color": "#00A39D", "id": "_K04 - Jasa Penunjang Keuangan", "label": "Jasa Penunjang Keuangan\nRp16,08 T", "physics": false, "shape": "dot", "size": 10.123654957280166, "title": "Jasa Penunjang Keuangan", "x": 325.30915970027144, "y": 379.7024476180696}, {"color": "#00A39D", "id": "_L - Real Estate", "label": "Real Estate\nRp505,46 T", "physics": false, "shape": "dot", "size": 13.888072035777892, "title": "Real Estate", "x": 369.50436408433944, "y": 336.84790068874014}, {"color": "#00A39D", "id": "_M - Jasa Perusahaan", "label": "Jasa Perusahaan\nRp383,09 T", "physics": false, "shape": "dot", "size": 12.946813922445335, "title": "Jasa Perusahaan", "x": 408.0984604688859, "y": 288.88693450260797}, {"color": "#00A39D", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial\nRp616,44 T", "physics": false, "shape": "dot", "size": 14.741804617465055, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": 440.5061610523411, "y": 236.546681012756}, {"color": "#00A39D", "id": "_P - Jasa Pendidikan", "label": "Jasa Pendidikan\nRp583,61 T", "physics": false, "shape": "dot", "size": 14.489254786205962, "title": "Jasa Pendidikan", "x": 466.2361149520492, "y": 180.62085055959392}, {"color": "#00A39D", "id": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "label": "Jasa Kesehatan \u0026 Kegiatan Sosial\nRp252,00 T", "physics": false, "shape": "dot", "size": 11.938419184105278, "title": "Jasa Kesehatan dan Kegiatan Sosial", "x": 484.89850779637567, "y": 121.95676786079198}, {"color": "#00A39D", "id": "_R - Jasa lainnya", "label": "Jasa lainnya\nRp405,19 T", "physics": false, "shape": "dot", "size": 13.116808143050033, "title": "Jasa lainnya", "x": 496.21024732873354, "y": 61.44417751408366}]);
                  edges = new vis.DataSet([{"from": "_A01a - Tanaman Pangan", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.717860245887305}, {"from": "_A01b - Tanaman Hortikultura", "to": "_C02 - Industri Makanan dan Minuman", "width": 0.7833274051204595}, {"from": "_A01b - Tanaman Hortikultura", "to": "_I02 - Penyediaan Makan Minum", "width": 0.8322927550206528}, {"from": "_A01c - Tanaman Perkebunan", "to": "_C02 - Industri Makanan dan Minuman", "width": 4.19789351821315}, {"from": "_A01c - Tanaman Perkebunan", "to": "_C03 - Industri Pengolahan Tembakau", "width": 0.8286578364340043}, {"from": "_A01c - Tanaman Perkebunan", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 1.0628044742765685}, {"from": "_A01d - Peternakan", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0563140095435255}, {"from": "_A01d - Peternakan", "to": "_I02 - Penyediaan Makan Minum", "width": 0.7465468437866173}, {"from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 0.9008037411387337}, {"from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_F - Konstruksi", "width": 1.1147102023977993}, {"from": "_A03 - Perikanan", "to": "_C02 - Industri Makanan dan Minuman", "width": 1.2368955347348312}, {"from": "_A03 - Perikanan", "to": "_I01 - Penyediaan Akomodasi", "width": 0.5550771740745869}, {"from": "_A03 - Perikanan", "to": "_I02 - Penyediaan Makan Minum", "width": 1.0381947712087722}, {"from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_C01 - Industri Batubara dan Pengilangan Migas", "width": 1.9465987796101045}, {"from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 1.140019092123949}, {"from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 1.3147023350005038}, {"from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_C11 - Industri Logam Dasar", "width": 0.6483944836839238}, {"from": "_B03 - Pertambangan Bijih Logam", "to": "_C11 - Industri Logam Dasar", "width": 2.562786417100872}, {"from": "_B03 - Pertambangan Bijih Logam", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 0.634861973446234}, {"from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 0.7649423681134444}, {"from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_F - Konstruksi", "width": 1.9566921470303296}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_C01 - Industri Batubara dan Pengilangan Migas", "width": 0.7248185202335337}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H02 - Angkutan Darat", "width": 1.267060612798478}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H03 - Angkutan Laut", "width": 0.6867338250480937}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 0.5255143523421305}, {"from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H05 - Angkutan Udara", "width": 1.2172811376520172}, {"from": "_C02 - Industri Makanan dan Minuman", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 0.5099634876093794}, {"from": "_C02 - Industri Makanan dan Minuman", "to": "_C02 - Industri Makanan dan Minuman", "width": 4.5}, {"from": "_C02 - Industri Makanan dan Minuman", "to": "_I01 - Penyediaan Akomodasi", "width": 0.8088235087915465}, {"from": "_C02 - Industri Makanan dan Minuman", "to": "_I02 - Penyediaan Makan Minum", "width": 3.337500041843687}, {"from": "_C02 - Industri Makanan dan Minuman", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 0.7601340884475634}, {"from": "_C02 - Industri Makanan dan Minuman", "to": "_R - Jasa lainnya", "width": 0.7262884807049527}, {"from": "_C03 - Industri Pengolahan Tembakau", "to": "_C03 - Industri Pengolahan Tembakau", "width": 0.7427041540115199}, {"from": "_C03 - Industri Pengolahan Tembakau", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 0.5015584698751321}, {"from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_C04 - Industri Tekstil dan Pakaian Jadi", "width": 1.736979615519804}, {"from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 0.574008272577899}, {"from": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 0.6844442286412161}, {"from": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "to": "_C15 - Industri Furnitur", "width": 0.5266053367154062}, {"from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 0.596500874710711}, {"from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_C15 - Industri Furnitur", "width": 0.5725121919093601}, {"from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_F - Konstruksi", "width": 1.3247590608306659}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "width": 1.1259693385109955}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 0.7844710232923646}, {"from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_P - Jasa Pendidikan", "width": 0.7349113311326405}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_A01a - Tanaman Pangan", "width": 0.6649489924506005}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_A01b - Tanaman Hortikultura", "width": 0.6294380036187888}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_A01c - Tanaman Perkebunan", "width": 0.7592788815342071}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 1.2033234141259839}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 1.2041122163290756}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 0.6567570892453197}, {"from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 0.521858412339407}, {"from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 0.6733653033801827}, {"from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 0.5523884571314378}, {"from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_F - Konstruksi", "width": 0.7724201972059497}, {"from": "_C10 - Industri Barang Galian bukan Logam", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 0.6159125844184956}, {"from": "_C10 - Industri Barang Galian bukan Logam", "to": "_F - Konstruksi", "width": 2.3627293672151914}, {"from": "_C11 - Industri Logam Dasar", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 1.1683822372962047}, {"from": "_C11 - Industri Logam Dasar", "to": "_F - Konstruksi", "width": 1.4673903763201186}, {"from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 0.9143252892290954}, {"from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_F - Konstruksi", "width": 1.6779921161236862}, {"from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 0.602292930685221}, {"from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_F - Konstruksi", "width": 0.599417059241003}, {"from": "_C14 - Industri Alat Angkutan", "to": "_C14 - Industri Alat Angkutan", "width": 1.1871438280442286}, {"from": "_C14 - Industri Alat Angkutan", "to": "_F - Konstruksi", "width": 0.7091611387000804}, {"from": "_C15 - Industri Furnitur", "to": "_C15 - Industri Furnitur", "width": 0.5121263569204095}, {"from": "_C15 - Industri Furnitur", "to": "_F - Konstruksi", "width": 0.5324373858937463}, {"from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 0.5364591803523964}, {"from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_H05 - Angkutan Udara", "width": 0.5436619674845027}, {"from": "_D01 - Ketenagalistrikan", "to": "_H01 - Angkutan Rel", "width": 0.5078828023537416}, {"from": "_D01 - Ketenagalistrikan", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 0.6314428915702}, {"from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 0.5424242209325384}, {"from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 0.5092437239541356}, {"from": "_F - Konstruksi", "to": "_F - Konstruksi", "width": 1.072714584589556}, {"from": "_F - Konstruksi", "to": "_L - Real Estate", "width": 1.2529835382031234}, {"from": "_F - Konstruksi", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 0.9943108069835058}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_C14 - Industri Alat Angkutan", "width": 0.8154097346755393}, {"from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_H02 - Angkutan Darat", "width": 1.011091522721164}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C02 - Industri Makanan dan Minuman", "width": 1.734799152463116}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 0.614056560411155}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 0.7115162380553902}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 1.0444317349200074}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 0.7606506402239093}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C15 - Industri Furnitur", "width": 0.589138346805313}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 0.6278497212355033}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_F - Konstruksi", "width": 3.3708481109480113}, {"from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_I02 - Penyediaan Makan Minum", "width": 1.234135713980299}, {"from": "_H01 - Angkutan Rel", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 0.5201673631622394}, {"from": "_H01 - Angkutan Rel", "to": "_M - Jasa Perusahaan", "width": 0.5171183398011646}, {"from": "_H02 - Angkutan Darat", "to": "_B03 - Pertambangan Bijih Logam", "width": 0.5478028038576573}, {"from": "_H02 - Angkutan Darat", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 0.5985725843196559}, {"from": "_H02 - Angkutan Darat", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 0.5124500001609946}, {"from": "_H02 - Angkutan Darat", "to": "_F - Konstruksi", "width": 2.0746015658861143}, {"from": "_H02 - Angkutan Darat", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 1.0964313867408753}, {"from": "_H03 - Angkutan Laut", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 0.6380834165522048}, {"from": "_H03 - Angkutan Laut", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 0.5846876604393086}, {"from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 0.5382664291226794}, {"from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_M - Jasa Perusahaan", "width": 0.5398793036134204}, {"from": "_H05 - Angkutan Udara", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 0.6894936054226649}, {"from": "_H05 - Angkutan Udara", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 0.9791140664204486}, {"from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 0.7722371194097695}, {"from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_F - Konstruksi", "width": 0.9489991266629728}, {"from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_H03 - Angkutan Laut", "width": 0.7045726448907654}, {"from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 0.5278168079267085}, {"from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_H05 - Angkutan Udara", "width": 0.965463850871102}, {"from": "_I01 - Penyediaan Akomodasi", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 0.6127316507517014}, {"from": "_I01 - Penyediaan Akomodasi", "to": "_P - Jasa Pendidikan", "width": 0.58593048616374}, {"from": "_I02 - Penyediaan Makan Minum", "to": "_F - Konstruksi", "width": 0.7068289696984779}, {"from": "_I02 - Penyediaan Makan Minum", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 0.832580937470645}, {"from": "_J - Informasi dan Komunikasi", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 0.6228636566312061}, {"from": "_J - Informasi dan Komunikasi", "to": "_K01 - Jasa Perantara Keuangan", "width": 0.7281076782622509}, {"from": "_J - Informasi dan Komunikasi", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 0.5675868578024601}, {"from": "_K01 - Jasa Perantara Keuangan", "to": "_A02 - Kehutanan dan Penebangan Kayu", "width": 0.5239103566380231}, {"from": "_K01 - Jasa Perantara Keuangan", "to": "_F - Konstruksi", "width": 0.7859691616836957}, {"from": "_K01 - Jasa Perantara Keuangan", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 0.6498814683211757}, {"from": "_K01 - Jasa Perantara Keuangan", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 1.3912406974049185}, {"from": "_K02 - Asuransi dan Dana Pensiun", "to": "_K01 - Jasa Perantara Keuangan", "width": 0.6190981929647651}, {"from": "_K02 - Asuransi dan Dana Pensiun", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 0.5491428667554793}, {"from": "_K04 - Jasa Penunjang Keuangan", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 0.5672123425919806}, {"from": "_M - Jasa Perusahaan", "to": "_F - Konstruksi", "width": 1.6203708061993316}, {"from": "_M - Jasa Perusahaan", "to": "_K01 - Jasa Perantara Keuangan", "width": 0.7713976950654136}, {"from": "_M - Jasa Perusahaan", "to": "_P - Jasa Pendidikan", "width": 0.8238095683011788}, {"from": "_M - Jasa Perusahaan", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 0.7589013816373097}, {"from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "to": "_H01 - Angkutan Rel", "width": 0.5222529401543399}, {"from": "_P - Jasa Pendidikan", "to": "_P - Jasa Pendidikan", "width": 0.5463701367913247}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 0.5317302075373999}, {"from": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 0.5953531341620527}, {"from": "_R - Jasa lainnya", "to": "_F - Konstruksi", "width": 0.7575147955836481}]);

                  nodeColors = {};
                  allNodes = nodes.get({ returnType: "Object" });
                  for (nodeId in allNodes) {
                    nodeColors[nodeId] = allNodes[nodeId].color;
                  }
                  allEdges = edges.get({ returnType: "Object" });
                  // adding nodes and edges to the graph
                  data = {nodes: nodes, edges: edges};

                  var options = {"nodes": {"borderWidth": 1, "borderWidthSelected": 2, "opacity": 100, "font": {"strokeWidth": 5, "size": 10}, "size": 25}, "edges": {"arrows": {"to": {"enabled": true, "scaleFactor": 0.5}}, "color": {"inherit": true}, "font": {"strokeWidth": 5}, "selfReferenceSize": 20, "selfReference": {"angle": 0.7853981633974483}, "smooth": {"forceDirection": "none"}}, "physics": {"barnesHut": {"springLength": 100}, "minVelocity": 0.75}};

                  


                  

                  network = new vis.Network(container, data, options);

                  

                  
                    network.on("selectNode", neighbourhoodHighlight);
                  

                  


                  

                  return network;

              }
              drawGraph();
        </script>
    </body>
</html>