---
layout: null
title: Dashboard
permalink: /dashboard/household-ecosystem
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
                                
                                    <option value="Ekosistem Rumah Tangga">Ekosistem Rumah Tangga</option>
                                
                                    <option value="Sektor Manufaktur">Sektor Manufaktur</option>
                                
                                    <option value="Industri Tekstil">Industri Tekstil</option>
                                
                                    <option value="Industri Tekstil Lainnya">Industri Tekstil Lainnya</option>
                                
                                    <option value="Industri Pembuatan Barang Tekstil">Industri Pembuatan Barang Tekstil</option>
                                
                                    <option value="Industri Barang Tekstil untuk Rumah Tangga">Industri Barang Tekstil untuk Rumah Tangga</option>
                                
                                    <option value="Industri Bahan Kimia">Industri Bahan Kimia</option>
                                
                                    <option value="Industri Barang Kimia Lainnya">Industri Barang Kimia Lainnya</option>
                                
                                    <option value="Industri Bahan Pembersih">Industri Bahan Pembersih</option>
                                
                                    <option value="Industri Sabun untuk Rumah Tangga">Industri Sabun untuk Rumah Tangga</option>
                                
                                    <option value="Industri Karet & Plastik">Industri Karet & Plastik</option>
                                
                                    <option value="Industri Karet">Industri Karet</option>
                                
                                    <option value="Industri Karet Lainnya">Industri Karet Lainnya</option>
                                
                                    <option value="Industri Karet untuk Rumah Tangga">Industri Karet untuk Rumah Tangga</option>
                                
                                    <option value="Industri Plastik">Industri Plastik</option>
                                
                                    <option value="Industri Plastik Lainnya">Industri Plastik Lainnya</option>
                                
                                    <option value="Industri Perlengkapan Rumah Tangga">Industri Perlengkapan Rumah Tangga</option>
                                
                                    <option value="Industri Barang Galian Bukan Logam">Industri Barang Galian Bukan Logam</option>
                                
                                    <option value="Industri Kaca">Industri Kaca</option>
                                
                                    <option value="Industri Barang dari Kaca">Industri Barang dari Kaca</option>
                                
                                    <option value="Industri Perlengkapan Rumah Tangga dari Kaca">Industri Perlengkapan Rumah Tangga dari Kaca</option>
                                
                                    <option value="Industri Barang Galian Bukan Logam Lainnya">Industri Barang Galian Bukan Logam Lainnya</option>
                                
                                    <option value="Industri Keramik dan Porselen">Industri Keramik dan Porselen</option>
                                
                                    <option value="Industri Perlengkapan Rumah Tangga dari Porselen">Industri Perlengkapan Rumah Tangga dari Porselen</option>
                                
                                    <option value="Industri Batu">Industri Batu</option>
                                
                                    <option value="Bahan Bangunan">Bahan Bangunan</option>
                                
                                    <option value="Industri Marmer untuk Rumah Tangga">Industri Marmer untuk Rumah Tangga</option>
                                
                                    <option value="Industri Barang Logam">Industri Barang Logam</option>
                                
                                    <option value="Industri Barang Logam Lainnya">Industri Barang Logam Lainnya</option>
                                
                                    <option value="Industri Alat Potong">Industri Alat Potong</option>
                                
                                    <option value="Industri Alat Potong untuk Rumah Tangga">Industri Alat Potong untuk Rumah Tangga</option>
                                
                                    <option value="Industri Barang Logam Lainnya YTDL">Industri Barang Logam Lainnya YTDL</option>
                                
                                    <option value="Industri Logam untuk Rumah Tangga">Industri Logam untuk Rumah Tangga</option>
                                
                                    <option value="Industri Peralatan Listrik">Industri Peralatan Listrik</option>
                                
                                    <option value="Industri Peralatan Rumah Tangga">Industri Peralatan Rumah Tangga</option>
                                
                                    <option value="Industri Peralatan Listrik Rumah Tangga">Industri Peralatan Listrik Rumah Tangga</option>
                                
                                    <option value="Industri Peralatan Elektrotermal Rumah Tangga">Industri Peralatan Elektrotermal Rumah Tangga</option>
                                
                                    <option value="Industri Peralatan Pemanas Rumah Tangga">Industri Peralatan Pemanas Rumah Tangga</option>
                                
                                    <option value="Sektor Perdagangan Besar & Eceran">Sektor Perdagangan Besar & Eceran</option>
                                
                                    <option value="Perdagangan Besar, Bukan Mobil & Sepeda Motor">Perdagangan Besar, Bukan Mobil & Sepeda Motor</option>
                                
                                    <option value="Perdagangan Besar Barang Keperluan Rumah Tangga">Perdagangan Besar Barang Keperluan Rumah Tangga</option>
                                
                                    <option value="Perdagangan Eceran">Perdagangan Eceran</option>
                                
                                    <option value="Perdagangan Perlengkapan Rumah Tangga di Toko">Perdagangan Perlengkapan Rumah Tangga di Toko</option>
                                
                                    <option value="Perdagangan Tekstil di Toko">Perdagangan Tekstil di Toko</option>
                                
                                    <option value="Perdagangan Perlengkapan Rumah Tangga dari Tekstil">Perdagangan Perlengkapan Rumah Tangga dari Tekstil</option>
                                
                                    <option value="Perdagangan Peralatan Listrik & Penerangan Rumah Tangga">Perdagangan Peralatan Listrik & Penerangan Rumah Tangga</option>
                                
                                    <option value="Perdagangan Furnitur, Peralatan Listrik, Penerangan Rumah Tangga">Perdagangan Furnitur, Peralatan Listrik, Penerangan Rumah Tangga</option>
                                
                                    <option value="Perdagangan Eceran Bukan di Toko & Pasar">Perdagangan Eceran Bukan di Toko & Pasar</option>
                                
                                    <option value="Perdagangan Eceran Bukan di Toko & Pasar Lainnya">Perdagangan Eceran Bukan di Toko & Pasar Lainnya</option>
                                
                                    <option value="Perdagangan Perlengkapan Rumah Tangga Keliling">Perdagangan Perlengkapan Rumah Tangga Keliling</option>
                                
                                    <option value="Perdagangan Melalui Pemesanan Pos/Internet">Perdagangan Melalui Pemesanan Pos/Internet</option>
                                
                                    <option value="Perdagangan Perlengkapan Melalui Media untuk Rumah Tangga">Perdagangan Perlengkapan Melalui Media untuk Rumah Tangga</option>
                                
                                    <option value="Perdagangan Eceran Kaki Lima & Pasar">Perdagangan Eceran Kaki Lima & Pasar</option>
                                
                                    <option value="Perdagangan Kaki Lima dan Pasar Perlengkapan Rumah Tangga">Perdagangan Kaki Lima dan Pasar Perlengkapan Rumah Tangga</option>
                                
                                    <option value="Perdagangan Kaki Lima dan Pasar Barang Lainnya & Barang Bekas">Perdagangan Kaki Lima dan Pasar Barang Lainnya & Barang Bekas</option>
                                
                                    <option value="Perlengkapan Rumah Tangga">Perlengkapan Rumah Tangga</option>
                                
                                    <option value="Perdagangan Barang Lainnya di Toko">Perdagangan Barang Lainnya di Toko</option>
                                
                                    <option value="Perdagangan Khusus Barang Bekas di Toko">Perdagangan Khusus Barang Bekas di Toko</option>
                                
                                    <option value="Barang Bekas Perlengkapan Rumah Tangga">Barang Bekas Perlengkapan Rumah Tangga</option>
                                
                                    <option value="Sektor Aktivitas Profesional, Ilmiah & Teknis">Sektor Aktivitas Profesional, Ilmiah & Teknis</option>
                                
                                    <option value="Aktivitas Profesional, Ilmiah & Teknis Lainnya">Aktivitas Profesional, Ilmiah & Teknis Lainnya</option>
                                
                                    <option value="Aktivitas Desain Khusus">Aktivitas Desain Khusus</option>
                                
                                    <option value="Aktivitas Desain Industri">Aktivitas Desain Industri</option>
                                
                                    <option value="Aktivitas Desain Peralatan Rumah Tangga & Furnitur">Aktivitas Desain Peralatan Rumah Tangga & Furnitur</option>
                                
                                    <option value="Aktivitas Penyewaan & Agen Perjalanan">Aktivitas Penyewaan & Agen Perjalanan</option>
                                
                                    <option value="Sektor Aktivitas Penyewaan">Sektor Aktivitas Penyewaan</option>
                                
                                    <option value="Aktivitas Penyewaan Barang Pribadi & Rumah Tangga">Aktivitas Penyewaan Barang Pribadi & Rumah Tangga</option>
                                
                                    <option value="Aktivitas Penyewaan Barang Pribadi & Rumah Tangga YTDL">Aktivitas Penyewaan Barang Pribadi & Rumah Tangga YTDL</option>
                                
                                    <option value="Aktivitas Penyewaan Barang Keperluan Rumah Tangga & Pribadi">Aktivitas Penyewaan Barang Keperluan Rumah Tangga & Pribadi</option>
                                
                                    <option value="Aktivitas Ketenagakerjaan">Aktivitas Ketenagakerjaan</option>
                                
                                    <option value="Aktivitas Penempatan Tenaga Kerja">Aktivitas Penempatan Tenaga Kerja</option>
                                
                                    <option value="Aktivitas Penempatan Tenaga Kerja (2)">Aktivitas Penempatan Tenaga Kerja (2)</option>
                                
                                    <option value="Aktivitas Penempatan Pekerja Rumah Tangga">Aktivitas Penempatan Pekerja Rumah Tangga</option>
                                
                                    <option value="Sektor Aktivitas Jasa Lainnya">Sektor Aktivitas Jasa Lainnya</option>
                                
                                    <option value="Reparasi Komputer & Perlengkapan Rumah Tangga">Reparasi Komputer & Perlengkapan Rumah Tangga</option>
                                
                                    <option value="Reparasi Peralatan Rumah Tangga">Reparasi Peralatan Rumah Tangga</option>
                                
                                    <option value="Reparasi Perlengkapan Rumah Tangga Lainnya">Reparasi Perlengkapan Rumah Tangga Lainnya</option>
                                
                                    <option value="Sektor Aktivitas Rumah Tangga sebagai Pemberi Kerja">Sektor Aktivitas Rumah Tangga sebagai Pemberi Kerja</option>
                                
                                    <option value="Aktivitas Rumah Tangga sebagai Pemberi Kerja dari Personil Domestik">Aktivitas Rumah Tangga sebagai Pemberi Kerja dari Personil Domestik</option>
                                
                                    <option value="Aktivitas Rumah Tangga untuk Kebutuhan Sendiri">Aktivitas Rumah Tangga untuk Kebutuhan Sendiri</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#2D2A32", "id": "Ekosistem Rumah Tangga", "label": "Ekosistem\nRumah Tangga", "shape": "dot", "size": 40.5, "title": "Ekosistem Rumah Tangga"}, {"color": "#F8AD3C", "id": "Sektor Manufaktur", "label": "Sektor Manufaktur", "shape": "dot", "size": 40.0, "title": "Industri Pengolahan"}, {"color": "#F8AD3C", "id": "Industri Tekstil", "label": "Industri Tekstil", "shape": "dot", "size": 39.5, "title": "Industri Tekstil"}, {"color": "#F8AD3C", "id": "Industri Tekstil Lainnya", "label": "Industri Tekstil\nLainnya", "shape": "dot", "size": 39.0, "title": "Industri Tekstil Lainnya"}, {"color": "#F8AD3C", "id": "Industri Pembuatan Barang Tekstil", "label": "Industri Pembuatan\nBarang Tekstil", "shape": "dot", "size": 38.5, "title": "Industri Pembuatan Barang Tekstil, Bukan Pakaian Jadi"}, {"color": "#F8AD3C", "id": "Industri Barang Tekstil untuk Rumah Tangga", "label": "Industri Barang Tekstil\nuntuk Rumah Tangga", "shape": "dot", "size": 38.0, "title": "Industri Barang Jadi Tekstil untuk Kebutuhan Rumah Tangga"}, {"color": "#F8AD3C", "id": "Industri Bahan Kimia", "label": "Industri\nBahan Kimia", "shape": "dot", "size": 37.5, "title": "Industri Bahan Kimia \u0026 Barang dari Bahan Kimia"}, {"color": "#F8AD3C", "id": "Industri Barang Kimia Lainnya", "label": "Industri Barang Kimia Lainnya", "shape": "dot", "size": 37.0, "title": "Industri Barang Kimia Lainnya"}, {"color": "#F8AD3C", "id": "Industri Bahan Pembersih", "label": "Industri\nBahan Pembersih", "shape": "dot", "size": 36.5, "title": "Industri Sabun \u0026 Deterjen, Bahan Pembersih \u0026 Pengilap, Parfum \u0026 Kosmetik"}, {"color": "#F8AD3C", "id": "Industri Sabun untuk Rumah Tangga", "label": "Industri Sabun\nuntuk Rumah Tangga", "shape": "dot", "size": 36.0, "title": "Industri Sabun \u0026 Bahan Pembersih Keperluan Rumah Tangga"}, {"color": "#F8AD3C", "id": "Industri Karet \u0026 Plastik", "label": "Industri\nKaret \u0026 Plastik", "shape": "dot", "size": 35.5, "title": "Industri Karet, Barang dari Karet \u0026 Plastik"}, {"color": "#F8AD3C", "id": "Industri Karet", "label": "Industri Karet", "shape": "dot", "size": 35.0, "title": "Industri Karet \u0026 Barang dari Karet"}, {"color": "#F8AD3C", "id": "Industri Karet Lainnya", "label": "Industri Karet \nLainnya", "shape": "dot", "size": 34.5, "title": "Industri Barang dari Karet Lainnya"}, {"color": "#F8AD3C", "id": "Industri Karet untuk Rumah Tangga", "label": "Industri Karet\nuntuk Rumah Tangga", "shape": "dot", "size": 34.0, "title": "Industri Barang dari karet untuk Keperluan Rumah Tangga"}, {"color": "#F8AD3C", "id": "Industri Plastik", "label": "Industri Plastik", "shape": "dot", "size": 33.5, "title": "Industri Barang dari Plastik"}, {"color": "#F8AD3C", "id": "Industri Plastik Lainnya", "label": "Industri Plastik\nLainnya", "shape": "dot", "size": 33.0, "title": "Industri Barang dari Plastik Lainnya"}, {"color": "#F8AD3C", "id": "Industri Perlengkapan Rumah Tangga", "label": "Industri Perlengkapan\nRumah Tangga", "shape": "dot", "size": 32.5, "title": "Industri Perlengkapan \u0026 Peralatan Rumah Tangga (Tidak Termasuk Furnitur)"}, {"color": "#F8AD3C", "id": "Industri Barang Galian Bukan Logam", "label": "Industri Barang Galian\nBukan Logam", "shape": "dot", "size": 32.0, "title": "Industri Barang Galian Bukan Logam"}, {"color": "#F8AD3C", "id": "Industri Kaca", "label": "Industri Kaca", "shape": "dot", "size": 31.5, "title": "Industri Kaca \u0026 Barang dari Kaca"}, {"color": "#F8AD3C", "id": "Industri Barang dari Kaca", "label": "Industri\nBarang dari Kaca", "shape": "dot", "size": 31.0, "title": "Industri Barang dari Kaca"}, {"color": "#F8AD3C", "id": "Industri Perlengkapan Rumah Tangga dari Kaca", "label": "Industri Perlengkapan\nRumah Tangga dari Kaca", "shape": "dot", "size": 30.5, "title": "Industri Perlengkapan Peralatan Rumah Tangga dari Kaca"}, {"color": "#F8AD3C", "id": "Industri Barang Galian Bukan Logam Lainnya", "label": "Industri Barang Galian\nBukan Logam Lainnya", "shape": "dot", "size": 30.0, "title": "Industri Barang Galian Bukan Logam Lainnya"}, {"color": "#F8AD3C", "id": "Industri Keramik dan Porselen", "label": "Industri Keramik dan Porselen", "shape": "dot", "size": 29.5, "title": "Industri Barang Tanah Liat/Keramik dan Porselen Bukan Bahan Bangunan"}, {"color": "#F8AD3C", "id": "Industri Perlengkapan Rumah Tangga dari Porselen", "label": "Industri Perlengkapan\nRumah Tangga dari Porselen", "shape": "dot", "size": 29.0, "title": "Industri Perlengkapan Rumah Tangga dari Porselen"}, {"color": "#F8AD3C", "id": "Industri Batu", "label": "Industri Batu", "shape": "dot", "size": 28.5, "title": "Industri Barang dari Batu"}, {"color": "#F8AD3C", "id": "Bahan Bangunan", "label": "Bahan Bangunan", "shape": "dot", "size": 28.0, "title": "Bahan Bangunan"}, {"color": "#F8AD3C", "id": "Industri Marmer untuk Rumah Tangga", "label": "Industri Marmer\nuntuk Rumah Tangga", "shape": "dot", "size": 27.5, "title": "Industri Barang dari Marmer \u0026 Granit untuk Keperluan Rumah Tangga \u0026 Pajangan"}, {"color": "#F8AD3C", "id": "Industri Barang Logam", "label": "Industri\nBarang Logam", "shape": "dot", "size": 27.0, "title": "Industri Barang Logam, Bukan Mesin \u0026 Peralatannya"}, {"color": "#F8AD3C", "id": "Industri Barang Logam Lainnya", "label": "Industri Barang\nLogam Lainnya", "shape": "dot", "size": 26.5, "title": "Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam"}, {"color": "#F8AD3C", "id": "Industri Alat Potong", "label": "Industri\nAlat Potong", "shape": "dot", "size": 26.0, "title": "Industri Alat Potong, Perkakas Tangan \u0026 Peralatan Umum"}, {"color": "#F8AD3C", "id": "Industri Alat Potong untuk Rumah Tangga", "label": "Industri Alat Potong\nuntuk Rumah Tangga", "shape": "dot", "size": 25.5, "title": "Industri Alat Potong \u0026 Perkakas Tangan yang Digunakan dalam Rumah Tangga"}, {"color": "#F8AD3C", "id": "Industri Barang Logam Lainnya YTDL", "label": "Industri Barang Logam\nLainnya YTDL", "shape": "dot", "size": 25.0, "title": "Industri Barang Logam Lainnya YTDL"}, {"color": "#F8AD3C", "id": "Industri Logam untuk Rumah Tangga", "label": "Industri Logam\nuntuk Rumah Tangga", "shape": "dot", "size": 24.5, "title": "Industri Keperluan Rumah Tangga dari Logam Bukan Perlatan Dapur dan Meja"}, {"color": "#F8AD3C", "id": "Industri Peralatan Listrik", "label": "Industri\nPeralatan Listrik", "shape": "dot", "size": 24.0, "title": "Industri Peralatan Listrik"}, {"color": "#F8AD3C", "id": "Industri Peralatan Rumah Tangga", "label": "Industri Peralatan\nRumah Tangga", "shape": "dot", "size": 23.5, "title": "Industri Peralatan Rumah Tangga"}, {"color": "#F8AD3C", "id": "Industri Peralatan Listrik Rumah Tangga", "label": "Industri Peralatan Listrik\nRumah Tangga", "shape": "dot", "size": 23.0, "title": "Industri Peralatan Listrik Rumah Tangga"}, {"color": "#F8AD3C", "id": "Industri Peralatan Elektrotermal Rumah Tangga", "label": "Industri Peralatan Elektrotermal\nRumah Tangga", "shape": "dot", "size": 22.5, "title": "Industri Peralatan Elektrotermal Rumah Tangga"}, {"color": "#F8AD3C", "id": "Industri Peralatan Pemanas Rumah Tangga", "label": "Industri Peralatan Pemanas\nRumah Tangga", "shape": "dot", "size": 22.0, "title": "Industri Peralatan Pemanas \u0026 Masak Bukan Listrik Rumah Tangga"}, {"color": "#DF99F0", "id": "Sektor Perdagangan Besar \u0026 Eceran", "label": "Sektor Perdagangan\nBesar \u0026 Eceran", "shape": "dot", "size": 21.5, "title": "Perdagangan Besar \u0026 Eceran: Reparasi \u0026 Perawatan Mobil \u0026 Sepeda Motor"}, {"color": "#DF99F0", "id": "Perdagangan Besar, Bukan Mobil \u0026 Sepeda Motor", "label": "Perdagangan Besar,\nBukan Mobil \u0026 Sepeda Motor", "shape": "dot", "size": 21.0, "title": "Perdagangan Besar, Bukan Mobil \u0026 Sepeda Motor"}, {"color": "#DF99F0", "id": "Perdagangan Besar Barang Keperluan Rumah Tangga", "label": "Perdagangan Besar\nBarang Keperluan\nRumah Tangga", "shape": "dot", "size": 20.5, "title": "Perdagangan Besar Barang Keperluan Rumah Tangga"}, {"color": "#DF99F0", "id": "Perdagangan Eceran", "label": "Perdagangan Eceran", "shape": "dot", "size": 20.0, "title": "Perdagangan Eceran, Bukan Mobil \u0026 Motor"}, {"color": "#DF99F0", "id": "Perdagangan Perlengkapan Rumah Tangga di Toko", "label": "Perdagangan Perlengkapan\nRumah Tangga di Toko", "shape": "dot", "size": 19.5, "title": "Perdagangan Eceran Khusus Perlengkapan Rumah Tangga Lainnya di Toko"}, {"color": "#DF99F0", "id": "Perdagangan Tekstil di Toko", "label": "Perdagangan Tekstil\ndi Toko", "shape": "dot", "size": 19.0, "title": "Perdagangan Ecerah Khusus Tekstil di Toko"}, {"color": "#DF99F0", "id": "Perdagangan Perlengkapan Rumah Tangga dari Tekstil", "label": "Perdagangan Perlengkapan\nRumah Tangga dari Tekstil", "shape": "dot", "size": 18.5, "title": "Perdagangan Eceran Perlengkapan Rumah Tangga dari Tekstil"}, {"color": "#DF99F0", "id": "Perdagangan Peralatan Listrik \u0026 Penerangan Rumah Tangga", "label": "Perdagangan Peralatan\nListrik \u0026 Penerangan\nRumah Tangga", "shape": "dot", "size": 18.0, "title": "Perdagangan Eceran Peralatan Listrik Rumah Tangga \u0026 Peralatan Penerangan \u0026 Perlengkapannya"}, {"color": "#DF99F0", "id": "Perdagangan Furnitur, Peralatan Listrik, Penerangan Rumah Tangga", "label": "Perdagangan Furnitur, Peralatan Listrik, Penerangan Rumah Tangga", "shape": "dot", "size": 17.5, "title": "Perdagangan Ecerah Khusus Furnitur, Peralatan Listrik Rumah Tangga, Peralatan Penerangan \u0026 Peralatan Rumah Tangga Lainnya di Toko"}, {"color": "#DF99F0", "id": "Perdagangan Eceran Bukan di Toko \u0026 Pasar", "label": "Perdagangan Eceran\nBukan di Toko \u0026 Pasar", "shape": "dot", "size": 17.0, "title": "Perdagangan Eceran Bukan di Toko, Kaki Lima \u0026 Los Pasar"}, {"color": "#DF99F0", "id": "Perdagangan Eceran Bukan di Toko \u0026 Pasar Lainnya", "label": "Perdagangan Eceran\nBukan di Toko \u0026 Pasar\nLainnya", "shape": "dot", "size": 16.5, "title": "Perdagangan Eceran Bukan di Toko, Kaki Lima \u0026 Los Pasar Lainnya"}, {"color": "#DF99F0", "id": "Perdagangan Perlengkapan Rumah Tangga Keliling", "label": "Perdagangan Perlengkapan\nRumah Tangga Keliling", "shape": "dot", "size": 16.0, "title": "Perdagangan Eceran Keliling Perlengkapan Rumah Tangga \u0026 Dapur"}, {"color": "#DF99F0", "id": "Perdagangan Melalui Pemesanan Pos/Internet", "label": "Perdagangan Melalui\nPemesanan Pos/Internet", "shape": "dot", "size": 15.5, "title": "Perdagangan Eceran Melalui Pemesanan Pos atau Internet"}, {"color": "#DF99F0", "id": "Perdagangan Perlengkapan Melalui Media untuk Rumah Tangga", "label": "Perdagangan Perlengkapan\nmelalui Media untuk Rumah Tangga", "shape": "dot", "size": 15.0, "title": "Perdagangan Eceran Melalui Media untuk Barang Perlengkapan Rumah Tangga \u0026 Dapur"}, {"color": "#DF99F0", "id": "Perdagangan Eceran Kaki Lima \u0026 Pasar", "label": "Perdagangan Eceran\nKaki Lima \u0026 Pasar", "shape": "dot", "size": 14.5, "title": "Perdagangan Eceran Kaki Lima \u0026 Los Pasar"}, {"color": "#DF99F0", "id": "Perdagangan Kaki Lima dan Pasar Perlengkapan Rumah Tangga", "label": "Perdagangan Kaki Lima \u0026 Pasar\nPerlengkapan Rumah Tangga", "shape": "dot", "size": 14.0, "title": "Perdagangan Eceran Kaki Lima \u0026 Los Pasar Perlengkapan Rumah Tangga"}, {"color": "#DF99F0", "id": "Perdagangan Kaki Lima dan Pasar Barang Lainnya \u0026 Barang Bekas", "label": "Perdagangan Kaki Lima \u0026 Pasar\nBarang Lainnya\n\u0026 Barang Bekas", "shape": "dot", "size": 13.5, "title": "Perdagangan Eceran Kaki Lima \u0026 Los Pasar Barang Lainnya \u0026 Barang Bekas"}, {"color": "#DF99F0", "id": "Perlengkapan Rumah Tangga", "label": "Perlengkapan Rumah Tangga", "shape": "dot", "size": 13.0, "title": "Perlengkapan Rumah Tangga"}, {"color": "#DF99F0", "id": "Perdagangan Barang Lainnya di Toko", "label": "Perdagangan Barang\nLainnya di Toko", "shape": "dot", "size": 12.5, "title": "Perdagangan Khusus Barang Lainnya di Toko"}, {"color": "#DF99F0", "id": "Perdagangan Khusus Barang Bekas di Toko", "label": "Perdagangan Khusus\nBarang Bekas\ndi Toko", "shape": "dot", "size": 12.0, "title": "Perdagangan Eceran Khusus Barang Bekas di Toko"}, {"color": "#DF99F0", "id": "Barang Bekas Perlengkapan Rumah Tangga", "label": "Barang Bekas\nPerlengkapan Rumah Tangga", "shape": "dot", "size": 11.5, "title": "Barang Bekas Perlengkapan Rumah Tangga"}, {"color": "#F05D5E", "id": "Sektor Aktivitas Profesional, Ilmiah \u0026 Teknis", "label": "Sektor Aktivitas Profesional,\nIlmiah \u0026 Teknis", "shape": "dot", "size": 11.0, "title": "Aktivitas Profesional, Ilmiah \u0026 Teknis"}, {"color": "#F05D5E", "id": "Aktivitas Profesional, Ilmiah \u0026 Teknis Lainnya", "label": "Aktivitas Profesional,\nIlmiah \u0026 Teknis Lainnya", "shape": "dot", "size": 10.5, "title": "Aktivitas Profesional, Ilmiah \u0026 Teknis Lainnya"}, {"color": "#F05D5E", "id": "Aktivitas Desain Khusus", "label": "Aktivitas\nDesain Khusus", "shape": "dot", "size": 10.0, "title": "Aktivitas Desain Khusus"}, {"color": "#F05D5E", "id": "Aktivitas Desain Industri", "label": "Aktivitas\nDesain Industri", "shape": "dot", "size": 10.0, "title": "Aktivitas Desain Industri"}, {"color": "#F05D5E", "id": "Aktivitas Desain Peralatan Rumah Tangga \u0026 Furnitur", "label": "Aktivitas Desain\nPeralatan Rumah Tangga\n\u0026 Furnitur", "shape": "dot", "size": 10.0, "title": "Aktivitas Desain Peralatan Rumah Tangga \u0026 Furnitur"}, {"color": "#DF99F0", "id": "Aktivitas Penyewaan \u0026 Agen Perjalanan", "label": "Aktivitas Penyewaan\n\u0026 Agen Perjalanan", "shape": "dot", "size": 10.0, "title": "Aktivitas Penyewaan \u0026 Sewa Guna Usaha Tanpa Hak Opsi Ketenagakerjaan, Agen Perjalanan \u0026 Penunjang Usaha Lainnya"}, {"color": "#DF99F0", "id": "Sektor Aktivitas Penyewaan", "label": "Sektor Aktivitas\nPenyewaan", "shape": "dot", "size": 10.0, "title": "Aktivitas Penyewaan \u0026 Sewa Guna Usaha Tanpa Hak Opsi"}, {"color": "#DF99F0", "id": "Aktivitas Penyewaan Barang Pribadi \u0026 Rumah Tangga", "label": "Aktivitas Penyewaan\nBarang Pribadi\n\u0026 Rumah Tangga", "shape": "dot", "size": 10.0, "title": "Aktivitas Penyewaan \u0026 Sewa Guna Usaha Tanpa Hak Opsi Barang Pribadi \u0026 Rumah Tangga"}, {"color": "#DF99F0", "id": "Aktivitas Penyewaan Barang Pribadi \u0026 Rumah Tangga YTDL", "label": "Aktivitas Penyewaan Barang Pribadi \u0026 Rumah Tangga YTDL", "shape": "dot", "size": 10.0, "title": "Aktivitas Penyewaan \u0026 Sewa Guna Usaha Tanpa Hak Opsi Barang Pribadi \u0026 Rumah Tangga Lain YTDL"}, {"color": "#DF99F0", "id": "Aktivitas Penyewaan Barang Keperluan Rumah Tangga \u0026 Pribadi", "label": "Aktivitas Penyewaan\nBarang Keperluan\nRumah Tangga \u0026 Pribadi", "shape": "dot", "size": 10.0, "title": "Aktivitas Penyewaan \u0026 Sewa Guna Usaha Tanpa Hak Opsi Barang Keperluan Rumah Tangga \u0026 Pribadi"}, {"color": "#DF99F0", "id": "Aktivitas Ketenagakerjaan", "label": "Aktivitas\nKetenagakerjaan", "shape": "dot", "size": 10.0, "title": "Aktivitas Ketenagakerjaan"}, {"color": "#DF99F0", "id": "Aktivitas Penempatan Tenaga Kerja", "label": "Aktivitas Penempatan\nTenaga Kerja", "shape": "dot", "size": 10.0, "title": "Aktivitas Penempatan Tenaga Kerja"}, {"color": "#DF99F0", "id": "Aktivitas Penempatan Tenaga Kerja (2)", "label": "Aktivitas Penempatan Tenaga Kerja (2)", "shape": "dot", "size": 10.0, "title": "Aktivitas Penempatan Tenaga Kerja (2)"}, {"color": "#DF99F0", "id": "Aktivitas Penempatan Pekerja Rumah Tangga", "label": "Aktivitas Penempatan\nPekerja Rumah Tangga", "shape": "dot", "size": 10.0, "title": "Aktivitas Penempatan Pekerja Rumah Tangga"}, {"color": "#4472C4", "id": "Sektor Aktivitas Jasa Lainnya", "label": "Sektor Aktivitas\nJasa Lainnya", "shape": "dot", "size": 10.0, "title": "Aktivitas Jasa Lainnya"}, {"color": "#4472C4", "id": "Reparasi Komputer \u0026 Perlengkapan Rumah Tangga", "label": "Reparasi Komputer \u0026 Perlengkapan\nRumah Tangga", "shape": "dot", "size": 10.0, "title": "Reparasi Komputer \u0026 Barang Keperluan Pribadi \u0026 Perlengkapan Rumah Tangga"}, {"color": "#4472C4", "id": "Reparasi Peralatan Rumah Tangga", "label": "Reparasi Peralatan\nRumah Tangga", "shape": "dot", "size": 10.0, "title": "Reparasi Peralatan Rumah Tangga \u0026 Peralatan Rumah \u0026 Kebun"}, {"color": "#4472C4", "id": "Reparasi Perlengkapan Rumah Tangga Lainnya", "label": "Reparasi Perlengkapan\nRumah Tangga Lainnya", "shape": "dot", "size": 10.0, "title": "Reparasi Barang Keperluan Pribadi \u0026 Perlengkapan Rumah Tangga Lainnya"}, {"color": "#4472C4", "id": "Sektor Aktivitas Rumah Tangga sebagai Pemberi Kerja", "label": "Sektor Aktivitas\nRumah Tangga\nsebagai Pemberi Kerja", "shape": "dot", "size": 10.0, "title": "Aktivitas Rumah Tangga Sebagai Pemberi Kerja; Aktivitas Yang Menghasilkan Barang Dan Jasa Oleh Rumah Tangga yang Digunakan untuk Memenuhi Kebutuhan Sendiri"}, {"color": "#4472C4", "id": "Aktivitas Rumah Tangga sebagai Pemberi Kerja dari Personil Domestik", "label": "Aktivitas Rumah Tangga\nsebagai Pemberi Kerja\ndari Personil Domestik", "shape": "dot", "size": 10.0, "title": "Aktivitas Rumah Tangga Sebagai Pemberi Kerja Dari Personil Domestik"}, {"color": "#4472C4", "id": "Aktivitas Rumah Tangga untuk Kebutuhan Sendiri", "label": "Aktivitas Rumah Tangga\nuntuk Kebutuhan Sendiri", "shape": "dot", "size": 10.0, "title": "Aktivitas Yang Menghasilkan Barang Dan Jasa Oleh Rumah\n Tangga Yang digunakan untuk Memenuhi Kebutuhan Sendiri\n"}]);
                  edges = new vis.DataSet([{"from": "Ekosistem Rumah Tangga", "label": "0.1", "to": "Sektor Manufaktur", "width": 2.0}, {"from": "Sektor Manufaktur", "label": "0.1", "to": "Industri Tekstil", "width": 2.0}, {"from": "Industri Tekstil", "label": "0.1", "to": "Industri Pembuatan Barang Tekstil", "width": 2.0}, {"from": "Industri Pembuatan Barang Tekstil", "label": "0.1", "to": "Industri Barang Tekstil untuk Rumah Tangga", "width": 2.0}, {"from": "Sektor Manufaktur", "label": "0.1", "to": "Industri Bahan Kimia", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Industri Bahan Pembersih", "width": 2.0}, {"from": "Industri Bahan Pembersih", "label": "0.1", "to": "Industri Sabun untuk Rumah Tangga", "width": 2.0}, {"from": "Sektor Manufaktur", "label": "0.1", "to": "Industri Karet \u0026 Plastik", "width": 2.0}, {"from": "Industri Karet \u0026 Plastik", "label": "0.1", "to": "Industri Karet", "width": 2.0}, {"from": "Industri Karet", "label": "0.1", "to": "Industri Karet Lainnya", "width": 2.0}, {"from": "Industri Karet Lainnya", "label": "0.1", "to": "Industri Karet untuk Rumah Tangga", "width": 2.0}, {"from": "Industri Karet \u0026 Plastik", "label": "0.1", "to": "Industri Plastik", "width": 2.0}, {"from": "Industri Plastik", "label": "0.1", "to": "Industri Plastik Lainnya", "width": 2.0}, {"from": "Industri Plastik Lainnya", "label": "0.1", "to": "Industri Perlengkapan Rumah Tangga", "width": 2.0}, {"from": "Sektor Manufaktur", "label": "0.1", "to": "Industri Barang Galian Bukan Logam", "width": 2.0}, {"from": "Industri Barang Galian Bukan Logam", "label": "0.1", "to": "Industri Kaca", "width": 2.0}, {"from": "Industri Kaca", "label": "0.1", "to": "Industri Barang dari Kaca", "width": 2.0}, {"from": "Industri Barang dari Kaca", "label": "0.1", "to": "Industri Perlengkapan Rumah Tangga dari Kaca", "width": 2.0}, {"from": "Industri Barang Galian Bukan Logam", "label": "0.1", "to": "Industri Barang Galian Bukan Logam Lainnya", "width": 2.0}, {"from": "Industri Barang Galian Bukan Logam Lainnya", "label": "0.1", "to": "Industri Keramik dan Porselen", "width": 2.0}, {"from": "Industri Keramik dan Porselen", "label": "0.1", "to": "Industri Perlengkapan Rumah Tangga dari Porselen", "width": 2.0}, {"from": "Industri Barang Galian Bukan Logam Lainnya", "label": "0.1", "to": "Industri Batu", "width": 2.0}, {"from": "Industri Batu", "label": "0.1", "to": "Bahan Bangunan", "width": 2.0}, {"from": "Industri Batu", "label": "0.1", "to": "Industri Marmer untuk Rumah Tangga", "width": 2.0}, {"from": "Sektor Manufaktur", "label": "0.1", "to": "Industri Barang Logam", "width": 2.0}, {"from": "Industri Barang Logam", "label": "0.1", "to": "Industri Barang Logam Lainnya", "width": 2.0}, {"from": "Industri Barang Logam Lainnya", "label": "0.1", "to": "Industri Alat Potong", "width": 2.0}, {"from": "Industri Alat Potong", "label": "0.1", "to": "Industri Alat Potong untuk Rumah Tangga", "width": 2.0}, {"from": "Industri Barang Logam Lainnya", "label": "0.1", "to": "Industri Barang Logam Lainnya YTDL", "width": 2.0}, {"from": "Industri Barang Logam Lainnya YTDL", "label": "0.1", "to": "Industri Logam untuk Rumah Tangga", "width": 2.0}, {"from": "Sektor Manufaktur", "label": "0.1", "to": "Industri Peralatan Listrik", "width": 2.0}, {"from": "Industri Peralatan Listrik", "label": "0.1", "to": "Industri Peralatan Rumah Tangga", "width": 2.0}, {"from": "Industri Peralatan Rumah Tangga", "label": "0.1", "to": "Industri Peralatan Listrik Rumah Tangga", "width": 2.0}, {"from": "Industri Peralatan Rumah Tangga", "label": "0.1", "to": "Industri Peralatan Elektrotermal Rumah Tangga", "width": 2.0}, {"from": "Industri Peralatan Rumah Tangga", "label": "0.1", "to": "Industri Peralatan Pemanas Rumah Tangga", "width": 2.0}, {"from": "Ekosistem Rumah Tangga", "label": "0.1", "to": "Sektor Perdagangan Besar \u0026 Eceran", "width": 2.0}, {"from": "Sektor Perdagangan Besar \u0026 Eceran", "label": "0.1", "to": "Perdagangan Besar, Bukan Mobil \u0026 Sepeda Motor", "width": 2.0}, {"from": "Perdagangan Besar, Bukan Mobil \u0026 Sepeda Motor", "label": "0.1", "to": "Perdagangan Besar Barang Keperluan Rumah Tangga", "width": 2.0}, {"from": "Sektor Perdagangan Besar \u0026 Eceran", "label": "0.1", "to": "Perdagangan Eceran", "width": 2.0}, {"from": "Perdagangan Eceran", "label": "0.1", "to": "Perdagangan Barang Lainnya di Toko", "width": 2.0}, {"from": "Perdagangan Barang Lainnya di Toko", "label": "0.1", "to": "Perdagangan Khusus Barang Bekas di Toko", "width": 2.0}, {"from": "Perdagangan Khusus Barang Bekas di Toko", "label": "0.1", "to": "Barang Bekas Perlengkapan Rumah Tangga", "width": 2.0}, {"from": "Perdagangan Eceran", "label": "0.1", "to": "Perdagangan Eceran Kaki Lima \u0026 Pasar", "width": 2.0}, {"from": "Perdagangan Eceran Kaki Lima \u0026 Pasar", "label": "0.1", "to": "Perdagangan Kaki Lima dan Pasar Perlengkapan Rumah Tangga", "width": 2.0}, {"from": "Perdagangan Eceran Kaki Lima \u0026 Pasar", "label": "0.1", "to": "Perdagangan Kaki Lima dan Pasar Barang Lainnya \u0026 Barang Bekas", "width": 2.0}, {"from": "Perdagangan Eceran", "label": "0.1", "to": "Perdagangan Eceran Bukan di Toko \u0026 Pasar", "width": 2.0}, {"from": "Perdagangan Eceran Bukan di Toko \u0026 Pasar", "label": "0.1", "to": "Perdagangan Melalui Pemesanan Pos/Internet", "width": 2.0}, {"from": "Perdagangan Melalui Pemesanan Pos/Internet", "label": "0.1", "to": "Perdagangan Perlengkapan Melalui Media untuk Rumah Tangga", "width": 2.0}, {"from": "Perdagangan Eceran Bukan di Toko \u0026 Pasar", "label": "0.1", "to": "Perdagangan Eceran Bukan di Toko \u0026 Pasar Lainnya", "width": 2.0}, {"from": "Perdagangan Eceran Bukan di Toko \u0026 Pasar Lainnya", "label": "0.1", "to": "Perdagangan Perlengkapan Rumah Tangga Keliling", "width": 2.0}, {"from": "Perdagangan Eceran", "label": "0.1", "to": "Perdagangan Perlengkapan Rumah Tangga di Toko", "width": 2.0}, {"from": "Perdagangan Perlengkapan Rumah Tangga di Toko", "label": "0.1", "to": "Perdagangan Tekstil di Toko", "width": 2.0}, {"from": "Perdagangan Tekstil di Toko", "label": "0.1", "to": "Perdagangan Perlengkapan Rumah Tangga dari Tekstil", "width": 2.0}, {"from": "Perdagangan Perlengkapan Rumah Tangga di Toko", "label": "0.1", "to": "Perdagangan Barang Lainnya di Toko", "width": 2.0}, {"from": "Perdagangan Perlengkapan Rumah Tangga di Toko", "label": "0.1", "to": "Perdagangan Peralatan Listrik \u0026 Penerangan Rumah Tangga", "width": 2.0}, {"from": "Ekosistem Rumah Tangga", "label": "0.1", "to": "Sektor Aktivitas Profesional, Ilmiah \u0026 Teknis", "width": 2.0}, {"from": "Sektor Aktivitas Profesional, Ilmiah \u0026 Teknis", "label": "0.1", "to": "Aktivitas Profesional, Ilmiah \u0026 Teknis Lainnya", "width": 2.0}, {"from": "Aktivitas Profesional, Ilmiah \u0026 Teknis Lainnya", "label": "0.1", "to": "Aktivitas Desain Khusus", "width": 2.0}, {"from": "Aktivitas Desain Khusus", "label": "0.1", "to": "Aktivitas Desain Industri", "width": 2.0}, {"from": "Aktivitas Desain Industri", "label": "0.1", "to": "Aktivitas Desain Peralatan Rumah Tangga \u0026 Furnitur", "width": 2.0}, {"from": "Ekosistem Rumah Tangga", "label": "0.1", "to": "Sektor Aktivitas Penyewaan", "width": 2.0}, {"from": "Sektor Aktivitas Penyewaan", "label": "0.1", "to": "Aktivitas Penyewaan \u0026 Agen Perjalanan", "width": 2.0}, {"from": "Aktivitas Penyewaan \u0026 Agen Perjalanan", "label": "0.1", "to": "Aktivitas Penyewaan Barang Pribadi \u0026 Rumah Tangga", "width": 2.0}, {"from": "Sektor Aktivitas Penyewaan", "label": "0.1", "to": "Aktivitas Ketenagakerjaan", "width": 2.0}, {"from": "Aktivitas Ketenagakerjaan", "label": "0.1", "to": "Aktivitas Penempatan Tenaga Kerja", "width": 2.0}, {"from": "Aktivitas Penempatan Tenaga Kerja", "label": "0.1", "to": "Aktivitas Penempatan Pekerja Rumah Tangga", "width": 2.0}, {"from": "Ekosistem Rumah Tangga", "label": "0.1", "to": "Sektor Aktivitas Jasa Lainnya", "width": 2.0}, {"from": "Sektor Aktivitas Jasa Lainnya", "label": "0.1", "to": "Reparasi Komputer \u0026 Perlengkapan Rumah Tangga", "width": 2.0}, {"from": "Reparasi Komputer \u0026 Perlengkapan Rumah Tangga", "label": "0.1", "to": "Reparasi Peralatan Rumah Tangga", "width": 2.0}, {"from": "Reparasi Komputer \u0026 Perlengkapan Rumah Tangga", "label": "0.1", "to": "Reparasi Perlengkapan Rumah Tangga Lainnya", "width": 2.0}, {"from": "Ekosistem Rumah Tangga", "label": "0.1", "to": "Sektor Aktivitas Rumah Tangga sebagai Pemberi Kerja", "width": 2.0}, {"from": "Sektor Aktivitas Rumah Tangga sebagai Pemberi Kerja", "label": "0.1", "to": "Aktivitas Rumah Tangga sebagai Pemberi Kerja dari Personil Domestik", "width": 2.0}, {"from": "Sektor Aktivitas Rumah Tangga sebagai Pemberi Kerja", "label": "0.1", "to": "Aktivitas Rumah Tangga untuk Kebutuhan Sendiri", "width": 2.0}]);

                  nodeColors = {};
                  allNodes = nodes.get({ returnType: "Object" });
                  for (nodeId in allNodes) {
                    nodeColors[nodeId] = allNodes[nodeId].color;
                  }
                  allEdges = edges.get({ returnType: "Object" });
                  // adding nodes and edges to the graph
                  data = {nodes: nodes, edges: edges};

                  var options = {"nodes": {"borderWidth": 1, "borderWidthSelected": 2, "opacity": 100, "font": {"strokeWidth": 2}, "size": 25}, "edges": {"color": {"inherit": true}, "selfReferenceSize": 20, "selfReference": {"angle": 0.7853981633974483}, "smooth": {"forceDirection": "none"}}, "physics": {"barnesHut": {"springLength": 140}, "minVelocity": 0.75}};

                  


                  

                  network = new vis.Network(container, data, options);

                  

                  
                    network.on("selectNode", neighbourhoodHighlight);
                  

                  


                  

                  return network;

              }
              drawGraph();
        </script>
    </body>
</html>