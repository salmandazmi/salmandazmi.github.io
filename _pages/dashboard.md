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
                 height: 600px;
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
                                
                                    <option value="Pertambangan Batu Bara">Pertambangan Batu Bara</option>
                                
                                    <option value="Batu Bara">Batu Bara</option>
                                
                                    <option value="Industri Batu Bara & Minyak Bumi">Industri Batu Bara & Minyak Bumi</option>
                                
                                    <option value="Industri Bahan Kimia">Industri Bahan Kimia</option>
                                
                                    <option value="Industri Barang Logam">Industri Barang Logam</option>
                                
                                    <option value="Aluminium Composite Panel">Aluminium Composite Panel</option>
                                
                                    <option value="Industri Karet & Plastik">Industri Karet & Plastik</option>
                                
                                    <option value="Industri Tekstil">Industri Tekstil</option>
                                
                                    <option value="Pengadaan Listrik & Gas">Pengadaan Listrik & Gas</option>
                                
                                    <option value="Bahan Bakar Gas">Bahan Bakar Gas</option>
                                
                                    <option value="Industri Barang Galian Bukan Logam">Industri Barang Galian Bukan Logam</option>
                                
                                    <option value="Industri Kertas">Industri Kertas</option>
                                
                                    <option value="Pertambangan Minyak Bumi & Gas Alam">Pertambangan Minyak Bumi & Gas Alam</option>
                                
                                    <option value="Minyak Bumi">Minyak Bumi</option>
                                
                                    <option value="Industri Komputer & Elektronik">Industri Komputer & Elektronik</option>
                                
                                    <option value="Industri Farmasi & Obat">Industri Farmasi & Obat</option>
                                
                                    <option value="Panas Bumi">Panas Bumi</option>
                                
                                    <option value="Industri Makanan">Industri Makanan</option>
                                
                                    <option value="Pertanian & Peternakan">Pertanian & Peternakan</option>
                                
                                    <option value="Industri Kayu & Gabus">Industri Kayu & Gabus</option>
                                
                                    <option value="Konstruksi">Konstruksi</option>
                                
                                    <option value="Etanol">Etanol</option>
                                
                                    <option value="Treatment Limbah & Sampah">Treatment Limbah & Sampah</option>
                                
                                    <option value="Aktivitas Olahraga & Rekreasi">Aktivitas Olahraga & Rekreasi</option>
                                
                                    <option value="Industri Mesin">Industri Mesin</option>
                                
                                    <option value="Penyediaan Makanan & Minuman">Penyediaan Makanan & Minuman</option>
                                
                                    <option value="Pertambangan Bijih Logam">Pertambangan Bijih Logam</option>
                                
                                    <option value="Uranium & Plutonium (Nuklir)">Uranium & Plutonium (Nuklir)</option>
                                
                                    <option value="Industri Minuman">Industri Minuman</option>
                                
                                    <option value="Administrasi Pemerintahan">Administrasi Pemerintahan</option>
                                
                                    <option value="Gas Alam">Gas Alam</option>
                                
                                    <option value="Pengelolaan Kehutanan">Pengelolaan Kehutanan</option>
                                
                                    <option value="Konstruksi Khusus">Konstruksi Khusus</option>
                                
                                    <option value="Dry Ice">Dry Ice</option>
                                
                                    <option value="Treatment Air">Treatment Air</option>
                                
                                    <option value="Air">Air</option>
                                
                                    <option value="Aktivitas Jasa Perorangan">Aktivitas Jasa Perorangan</option>
                                
                                    <option value="Briket">Briket</option>
                                
                                    <option value="Pupuk">Pupuk</option>
                                
                                    <option value="Isolasi Bangunan">Isolasi Bangunan</option>
                                
                                    <option value="Cat">Cat</option>
                                
                                    <option value="Sampo">Sampo</option>
                                
                                    <option value="Sabun">Sabun</option>
                                
                                    <option value="Kain">Kain</option>
                                
                                    <option value="Listrik">Listrik</option>
                                
                                    <option value="Coal Water Mixture">Coal Water Mixture</option>
                                
                                    <option value="Semen">Semen</option>
                                
                                    <option value="Aluminium">Aluminium</option>
                                
                                    <option value="Kertas">Kertas</option>
                                
                                    <option value="Alat Rumah Tangga dari Plastik">Alat Rumah Tangga dari Plastik</option>
                                
                                    <option value="Bahan Bakar Minyak">Bahan Bakar Minyak</option>
                                
                                    <option value="Pestisida">Pestisida</option>
                                
                                    <option value="Insektisida">Insektisida</option>
                                
                                    <option value="TV">TV</option>
                                
                                    <option value="Ponsel">Ponsel</option>
                                
                                    <option value="Komputer">Komputer</option>
                                
                                    <option value="Atap Aspal (Bitumen)">Atap Aspal (Bitumen)</option>
                                
                                    <option value="Pipa">Pipa</option>
                                
                                    <option value="Aspal">Aspal</option>
                                
                                    <option value="Polyester">Polyester</option>
                                
                                    <option value="Nilon">Nilon</option>
                                
                                    <option value="Produk Pembersih">Produk Pembersih</option>
                                
                                    <option value="Obat">Obat</option>
                                
                                    <option value="Alat Pelindung">Alat Pelindung</option>
                                
                                    <option value="Alat Keamanan Tenaga Medis">Alat Keamanan Tenaga Medis</option>
                                
                                    <option value="Parfum">Parfum</option>
                                
                                    <option value="Cat Kuku">Cat Kuku</option>
                                
                                    <option value="Make Up">Make Up</option>
                                
                                    <option value="Pewarna Rambut">Pewarna Rambut</option>
                                
                                    <option value="Pasta Gigi">Pasta Gigi</option>
                                
                                    <option value="Jamur">Jamur</option>
                                
                                    <option value="Pulp">Pulp</option>
                                
                                    <option value="Kayu">Kayu</option>
                                
                                    <option value="Blok Beton">Blok Beton</option>
                                
                                    <option value="Daging">Daging</option>
                                
                                    <option value="Gula">Gula</option>
                                
                                    <option value="Biofuel">Biofuel</option>
                                
                                    <option value="Biogas">Biogas</option>
                                
                                    <option value="Buah dan Sayur Kering">Buah dan Sayur Kering</option>
                                
                                    <option value="Pemandian Air Panas">Pemandian Air Panas</option>
                                
                                    <option value="Pompa Panas Geothermal">Pompa Panas Geothermal</option>
                                
                                    <option value="Restoran">Restoran</option>
                                
                                    <option value="Pengobatan Kanker">Pengobatan Kanker</option>
                                
                                    <option value="Penelitian">Penelitian</option>
                                
                                    <option value="Air Bersih">Air Bersih</option>
                                
                                    <option value="Pertahanan">Pertahanan</option>
                                
                                    <option value="Angkutan Darat">Angkutan Darat</option>
                                
                                    <option value="Industri Logam Dasar">Industri Logam Dasar</option>
                                
                                    <option value="Alat Pemadam Kebakaran">Alat Pemadam Kebakaran</option>
                                
                                    <option value="Rekayasa Hujan">Rekayasa Hujan</option>
                                
                                    <option value="LPG">LPG</option>
                                
                                    <option value="Pemanas/Pendingin Rumah">Pemanas/Pendingin Rumah</option>
                                
                                    <option value="Soft Drink">Soft Drink</option>
                                
                                    <option value="Laundry">Laundry</option>
                                
                                    <option value="Aquadest">Aquadest</option>
                                
                                    <option value="Aquabidest">Aquabidest</option>
                                
                                    <option value="Industri Furnitur">Industri Furnitur</option>
                                
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
                  nodes = new vis.DataSet([{"color": "#E5FDE8", "id": "Pertambangan Batu Bara", "label": "Pertambangan Batu Bara", "shape": "dot", "title": "Pertambangan Batu Bara dan Lignit", "x": 257, "y": 67}, {"color": "#FEFFE8", "id": "Batu Bara", "label": "Batu Bara", "physics": false, "shape": "dot", "title": "Batu Bara", "x": 191, "y": -90}, {"color": "#FFECEA", "id": "Industri Batu Bara \u0026 Minyak Bumi", "label": "Industri Batu Bara \u0026 Minyak Bumi", "shape": "dot", "title": "Industri Produk dari Batu Bara dan Pengilangan Minyak Bumi", "x": 377, "y": -362}, {"color": "#FFECEA", "id": "Industri Bahan Kimia", "label": "Industri Bahan Kimia", "shape": "dot", "title": "Industri Bahan Kimia dan Barang dari Bahan Kimia", "x": -254, "y": 277}, {"color": "#FFECEA", "id": "Industri Barang Logam", "label": "Industri Barang Logam", "physics": false, "shape": "dot", "title": "Industri Barang Logam, Bukan Mesin dan Peralatannya", "x": 189, "y": 498}, {"color": "#EEEEFF", "id": "Aluminium Composite Panel", "label": "Aluminium Composite Panel", "shape": "dot", "title": "Aluminium Composite Panel\nBahan Baku Pendukung: Batu Bara\nFungsi: Bangunan Tahan Air", "x": 236, "y": 744}, {"color": "#FFECEA", "id": "Industri Karet \u0026 Plastik", "label": "Industri Karet \u0026 Plastik", "shape": "dot", "title": "Industri Karet, Barang dari Karet dan Plastik\nBahan Baku Pendukung: Gas Alam", "x": 37, "y": -611}, {"color": "#FFECEA", "id": "Industri Tekstil", "label": "Industri Tekstil", "shape": "dot", "title": "Industri Tekstil", "x": 514, "y": -275}, {"color": "#E7F4FF", "id": "Pengadaan Listrik \u0026 Gas", "label": "Pengadaan Listrik \u0026 Gas", "physics": false, "shape": "dot", "title": "Pengadaan Listrik, Gas, Uap/Air Panas dan Udara Dingin\nPLTU: Batu Bara, Minyak Bumi \nPLTP: Panas Bumi\nPLTN: Nuklir\nPLTG: Gas Alam\nPLTA: Air", "x": 81, "y": -171}, {"color": "#FEFFE8", "id": "Bahan Bakar Gas", "label": "Bahan Bakar Gas", "physics": false, "shape": "dot", "title": "Bahan Bakar Gas\nBahan Baku: Gasifikasi Batu Bara, Gas Alam", "x": 25, "y": 313}, {"color": "#FFECEA", "id": "Industri Barang Galian Bukan Logam", "label": "Industri Barang Galian Bukan Logam", "physics": false, "shape": "dot", "title": "Industri Barang Galian Bukan Logam\nBahan Baku Pendukung: Panas Bumi (Pemurnian Mineral)", "x": 484, "y": 338}, {"color": "#FFECEA", "id": "Industri Kertas", "label": "Industri Kertas", "shape": "dot", "title": "Industri Kertas dan Barang Dari Kertas", "x": 342, "y": 507}, {"color": "#E5FDE8", "id": "Pertambangan Minyak Bumi \u0026 Gas Alam", "label": "Pertambangan Minyak Bumi \u0026 Gas Alam", "shape": "dot", "title": "Pertambangan Minyak Bumi dan Gas Alam dan Panas Bumi", "x": -94, "y": -563}, {"color": "#FEFFE8", "id": "Minyak Bumi", "label": "Minyak Bumi", "physics": false, "shape": "dot", "title": "Minyak Bumi", "x": 51, "y": -443}, {"color": "#FFECEA", "id": "Industri Komputer \u0026 Elektronik", "label": "Industri Komputer \u0026 Elektronik", "shape": "dot", "title": "Industri Komputer, Barang Elektronik dan Optik", "x": 262, "y": -747}, {"color": "#FFECEA", "id": "Industri Farmasi \u0026 Obat", "label": "Industri Farmasi \u0026 Obat", "shape": "dot", "title": "Industri Farmasi, Produk Obat Kimia dan Obat Tradisional", "x": -562, "y": -391}, {"color": "#FEFFE8", "id": "Panas Bumi", "label": "Panas Bumi", "physics": false, "shape": "dot", "title": "Panas Bumi", "x": 507, "y": 76}, {"color": "#FFECEA", "id": "Industri Makanan", "label": "Industri Makanan", "shape": "dot", "title": "Industri Makanan\nBahan Baku Pendukung: Panas Bumi (Pasteurisasi), Nuklir (Pengawetan)", "x": 47, "y": 464}, {"color": "#E5FDE8", "id": "Pertanian \u0026 Peternakan", "label": "Pertanian \u0026 Peternakan", "shape": "dot", "title": "Pertanian Tanaman, Peternakan dan Perburuan\nBahan Baku Pendukung: Panas Bumi (Rumah Kaca \u0026 Sterilisasi)", "x": 640, "y": 381}, {"color": "#FFECEA", "id": "Industri Kayu \u0026 Gabus", "label": "Industri Kayu \u0026 Gabus", "shape": "dot", "title": "Industri Kayu, Barang dari Kayu dan Gabus", "x": 561, "y": 429}, {"color": "#E7F4FF", "id": "Konstruksi", "label": "Konstruksi", "shape": "dot", "title": "Konstruksi", "x": 776, "y": -23}, {"color": "#FEFFE8", "id": "Etanol", "label": "Etanol", "shape": "dot", "title": "Etanol\nBahan Baku Pendukung: Panas Bumi", "x": 80, "y": -7}, {"color": "#E7F4FF", "id": "Treatment Limbah \u0026 Sampah", "label": "Treatment Limbah \u0026 Sampah", "shape": "dot", "title": "Pengumpulan, Treatment dan Pembuangan Limbah dan Sampah", "x": 729, "y": -119}, {"color": "#EEEEFF", "id": "Aktivitas Olahraga \u0026 Rekreasi", "label": "Aktivitas Olahraga \u0026 Rekreasi", "shape": "dot", "title": "Aktivitas Olahraga dan Rekreasi Lainnya", "x": 784, "y": 92}, {"color": "#FFECEA", "id": "Industri Mesin", "label": "Industri Mesin", "shape": "dot", "title": "Industri Mesin dan Perlengkapan", "x": 712, "y": 308}, {"color": "#EEEEFF", "id": "Penyediaan Makanan \u0026 Minuman", "label": "Penyediaan Makanan \u0026 Minuman", "shape": "dot", "title": "Penyediaan Makanan dan Minuman", "x": 763, "y": 206}, {"color": "#E5FDE8", "id": "Pertambangan Bijih Logam", "label": "Pertambangan Bijih Logam", "shape": "dot", "title": "Pertambangan Bijih Logam", "x": -426, "y": -246}, {"color": "#FEFFE8", "id": "Uranium \u0026 Plutonium (Nuklir)", "label": "Uranium \u0026 Plutonium (Nuklir)", "physics": false, "shape": "dot", "title": "Uranium \u0026 Plutonium (Nuklir)", "x": -168, "y": -248}, {"color": "#FFECEA", "id": "Industri Minuman", "label": "Industri Minuman", "shape": "dot", "title": "Industri Minuman", "x": -345, "y": -653}, {"color": "#E7F4FF", "id": "Administrasi Pemerintahan", "label": "Administrasi Pemerintahan", "shape": "dot", "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "x": -591, "y": -156}, {"color": "#FEFFE8", "id": "Gas Alam", "label": "Gas Alam", "physics": false, "shape": "dot", "title": "Gas Alam", "x": -165, "y": -342}, {"color": "#E5FDE8", "id": "Pengelolaan Kehutanan", "label": "Pengelolaan Kehutanan", "shape": "dot", "title": "Pengelolaan Kehutanan dan Penebangan", "x": -446, "y": -582}, {"color": "#E7F4FF", "id": "Konstruksi Khusus", "label": "Konstruksi Khusus", "shape": "dot", "title": "Konstruksi Khusus", "x": -233, "y": -691}, {"color": "#EEEEFF", "id": "Dry Ice", "label": "Dry Ice", "shape": "dot", "title": "Dry Ice\nBahan Baku: Gas Alam", "x": -123, "y": 566}, {"color": "#E7F4FF", "id": "Treatment Air", "label": "Treatment Air", "shape": "dot", "title": "Treatment Air", "x": -500, "y": -77}, {"color": "#FEFFE8", "id": "Air", "label": "Air", "physics": false, "shape": "dot", "title": "Air", "x": -237, "y": 18}, {"color": "#E7F4FF", "id": "Aktivitas Jasa Perorangan", "label": "Aktivitas Jasa Perorangan", "shape": "dot", "title": "Aktivitas Jasa perorangan lainnya", "x": -667, "y": 123}, {"color": "#EEEEFF", "id": "Briket", "label": "Briket", "shape": "dot", "title": "Briket\nBahan Baku: Batu Bara", "x": 548, "y": -571}, {"color": "#EEEEFF", "id": "Pupuk", "label": "Pupuk", "shape": "dot", "title": "Pupuk\nBahan Baku: Sisa Olahan Batu Bara\nBahan Baku Pendukung: Minyak Bumi\nBahan Bakar: Gas Khusus (BBG Batu Bara)", "x": -495, "y": 474}, {"color": "#EEEEFF", "id": "Isolasi Bangunan", "label": "Isolasi Bangunan", "shape": "dot", "title": "Isolasi Bangunan\nBahan Baku Pendukung: Batu Bara", "x": 94, "y": -812}, {"color": "#EEEEFF", "id": "Cat", "label": "Cat", "shape": "dot", "title": "Cat\nBahan Baku Pendukung: Batu Bara", "x": -420, "y": 554}, {"color": "#EEEEFF", "id": "Sampo", "label": "Sampo", "shape": "dot", "title": "Sampo\nBahan Baku Pendukung: Batu Bara, Minyak Bumi", "x": -392, "y": 639}, {"color": "#EEEEFF", "id": "Sabun", "label": "Sabun", "shape": "dot", "title": "Sabun\nBahan Baku Pendukung: Batu Bara", "x": -602, "y": 288}, {"color": "#EEEEFF", "id": "Kain", "label": "Kain", "shape": "dot", "title": "Kain\nBahan Baku Pendukung: Batu Bara, Panas Bumi (Pewarnaan)", "x": 722, "y": -288}, {"color": "#FEFFE8", "id": "Listrik", "label": "Listrik", "physics": false, "shape": "dot", "title": "Listrik", "x": -10, "y": -33}, {"color": "#EEEEFF", "id": "Coal Water Mixture", "label": "Coal Water Mixture", "shape": "dot", "title": "Coal Water Mixture", "x": 380, "y": -66}, {"color": "#EEEEFF", "id": "Semen", "label": "Semen", "shape": "dot", "title": "Semen\nBahan Baku Pendukung: Panas Bumi (Pengeringan)\nBahan Bakar: Gasifikasi Batu Bara, Gas Alam", "x": 482, "y": 536}, {"color": "#EEEEFF", "id": "Aluminium", "label": "Aluminium", "shape": "dot", "title": "Aluminium\nBahan Bakar: Kokast Gas (BBG Batu Bara)", "x": 161, "y": 778}, {"color": "#EEEEFF", "id": "Kertas", "label": "Kertas", "shape": "dot", "title": "Kertas\nBahan Bakar: BBG Batu Bara", "x": 433, "y": 723}, {"color": "#EEEEFF", "id": "Alat Rumah Tangga dari Plastik", "label": "Alat Rumah Tangga dari Plastik", "shape": "dot", "title": "Alat Rumah Tangga dari Plastik\nBahan Baku: Minyak Bumi", "x": -2, "y": -847}, {"color": "#FEFFE8", "id": "Bahan Bakar Minyak", "label": "Bahan Bakar Minyak", "physics": false, "shape": "dot", "title": "Bahan Bakar Minyak\nSolar, Bensin, Bensol, Minyak Tanah", "x": 518, "y": -84}, {"color": "#EEEEFF", "id": "Pestisida", "label": "Pestisida", "shape": "dot", "title": "Pestisida\nBahan Baku Pendukung: Minyak Bumi", "x": -601, "y": 399}, {"color": "#EEEEFF", "id": "Insektisida", "label": "Insektisida", "shape": "dot", "title": "Insektisida\nBahan Baku Pendukung: Minyak Bumi", "x": -543, "y": 220}, {"color": "#EEEEFF", "id": "TV", "label": "TV", "shape": "dot", "title": "TV\nBahan Baku Pendukung: Minyak Bumi", "x": 364, "y": -894}, {"color": "#EEEEFF", "id": "Ponsel", "label": "Ponsel", "shape": "dot", "title": "Ponsel\nBahan Baku Pendukung: Minyak Bumi", "x": 207, "y": -923}, {"color": "#EEEEFF", "id": "Komputer", "label": "Komputer", "shape": "dot", "title": "Komputer\nBahan Baku Pendukung: Minyak Bumi", "x": 450, "y": -794}, {"color": "#EEEEFF", "id": "Atap Aspal (Bitumen)", "label": "Atap Aspal (Bitumen)", "shape": "dot", "title": "Atap Aspal (Bitumen)\nBahan Baku Pendukung: Minyak Bumi", "x": 572, "y": -491}, {"color": "#EEEEFF", "id": "Pipa", "label": "Pipa", "shape": "dot", "title": "Pipa\nBahan Baku Pendukung: Minyak Bumi", "x": -85, "y": -803}, {"color": "#EEEEFF", "id": "Aspal", "label": "Aspal", "shape": "dot", "title": "Aspal\nBahan Baku Pendukung: Minyak Bumi", "x": 357, "y": -573}, {"color": "#EEEEFF", "id": "Polyester", "label": "Polyester", "shape": "dot", "title": "Polyester\nBahan Baku Pendukung: Minyak Bumi (Bahan Serat Kain Sintetis)", "x": 734, "y": -387}, {"color": "#EEEEFF", "id": "Nilon", "label": "Nilon", "shape": "dot", "title": "Nilon\nBahan Baku Pendukung: Minyak Bumi (Bahan Serat Kain Sintetis)", "x": 684, "y": -461}, {"color": "#EEEEFF", "id": "Produk Pembersih", "label": "Produk Pembersih", "shape": "dot", "title": "Produk Pembersih\nBahan Baku Pendukung: Minyak Bumi", "x": -809, "y": -299}, {"color": "#EEEEFF", "id": "Obat", "label": "Obat", "shape": "dot", "title": "Obat\nBahan Baku Pendukung: Minyak Bumi", "x": -803, "y": -393}, {"color": "#EEEEFF", "id": "Alat Pelindung", "label": "Alat Pelindung", "shape": "dot", "title": "Alat Pelindung\nBahan Baku Pendukung: Minyak Bumi", "x": -681, "y": -562}, {"color": "#EEEEFF", "id": "Alat Keamanan Tenaga Medis", "label": "Alat Keamanan Tenaga Medis", "shape": "dot", "title": "Alat Keamanan Tenaga Medis\nBahan Baku Pendukung: Minyak Bumi", "x": -765, "y": -490}, {"color": "#EEEEFF", "id": "Parfum", "label": "Parfum", "shape": "dot", "title": "Parfum\nBahan Baku Pendukung: Minyak Bumi", "x": -325, "y": 588}, {"color": "#EEEEFF", "id": "Cat Kuku", "label": "Cat Kuku", "shape": "dot", "title": "Cat Kuku\nBahan Baku Pendukung: Minyak Bumi", "x": -568, "y": 487}, {"color": "#EEEEFF", "id": "Make Up", "label": "Make Up", "shape": "dot", "title": "Make Up\nBahan Baku Pendukung: Minyak Bumi", "x": -224, "y": 570}, {"color": "#EEEEFF", "id": "Pewarna Rambut", "label": "Pewarna Rambut", "shape": "dot", "title": "Pewarna Rambut\nBahan Baku Pendukung: Minyak Bumi", "x": -544, "y": 345}, {"color": "#EEEEFF", "id": "Pasta Gigi", "label": "Pasta Gigi", "shape": "dot", "title": "Pasta Gigi\nBahan Baku Pendukung: Minyak Bumi", "x": -497, "y": 574}, {"color": "#EEEEFF", "id": "Jamur", "label": "Jamur", "shape": "dot", "title": "Jamur\nBahan Baku Pendukung: Panas Bumi (Sterilisasi pada Proses Budidaya)", "x": 710, "y": 579}, {"color": "#EEEEFF", "id": "Pulp", "label": "Pulp", "shape": "dot", "title": "Pulp\nBahan Baku Pendukung: Panas Bumi (Pengeringan)", "x": 347, "y": 758}, {"color": "#EEEEFF", "id": "Kayu", "label": "Kayu", "shape": "dot", "title": "Kayu\nBahan Baku Pendukung: Panas Bumi (Pengeringan)", "x": 591, "y": 652}, {"color": "#EEEEFF", "id": "Blok Beton", "label": "Blok Beton", "shape": "dot", "title": "Blok Beton\nBahan Baku Pendukung: Panas Bumi (Perawatan)", "x": 944, "y": -94}, {"color": "#EEEEFF", "id": "Daging", "label": "Daging", "shape": "dot", "title": "Daging\nBahan Baku Pendukung: Panas Bumi (Pengeringan)", "x": -9, "y": 773}, {"color": "#EEEEFF", "id": "Gula", "label": "Gula", "shape": "dot", "title": "Gula\nBahan Baku Pendukung: Panas Bumi (Pengeringan)", "x": -83, "y": 739}, {"color": "#FEFFE8", "id": "Biofuel", "label": "Biofuel", "shape": "dot", "title": "Biofuel\nBahan Baku Pendukung: Panas Bumi", "x": 461, "y": -590}, {"color": "#FEFFE8", "id": "Biogas", "label": "Biogas", "shape": "dot", "title": "Biogas\nBahan Baku Pendukung: Panas Bumi", "x": 880, "y": -246}, {"color": "#EEEEFF", "id": "Buah dan Sayur Kering", "label": "Buah dan Sayur Kering", "shape": "dot", "title": "Buah dan Sayur Kering\nBahan Baku Pendukung: Panas Bumi (Pengeringan)", "x": 62, "y": 734}, {"color": "#EEEEFF", "id": "Pemandian Air Panas", "label": "Pemandian Air Panas", "shape": "dot", "title": "Pemandian Air Panas\nBahan Baku Pendukung: Panas Bumi", "x": 958, "y": 100}, {"color": "#EEEEFF", "id": "Pompa Panas Geothermal", "label": "Pompa Panas Geothermal", "shape": "dot", "title": "Pompa Panas Geothermal\nBahan Bakar: Panas Bumi", "x": 822, "y": 465}, {"color": "#EEEEFF", "id": "Restoran", "label": "Restoran", "shape": "dot", "title": "Restoran\nBahan Bakar: Panas Bumi (Memasak)", "x": 913, "y": 300}, {"color": "#EEEEFF", "id": "Pengobatan Kanker", "label": "Pengobatan Kanker", "shape": "dot", "title": "Pengobatan Kanker\nBahan Baku Pendukung: Nuklir", "x": -744, "y": -244}, {"color": "#FFE8E8", "id": "Penelitian", "label": "Penelitian", "shape": "dot", "title": "Penelitian dan Pengembangan Ilmu Pengetahuan", "x": -315, "y": -309}, {"color": "#EEEEFF", "id": "Air Bersih", "label": "Air Bersih", "shape": "dot", "title": "Air Bersih\nBahan Baku Pendukung: Nuklir (Desalinasi Air Laut)", "x": -737, "y": 3}, {"color": "#EEEEFF", "id": "Pertahanan", "label": "Pertahanan", "shape": "dot", "title": "Pertahanan\nBahan Bakar: Nuklir (Kapal Selam dan Roket)", "x": -824, "y": -85}, {"color": "#E7F4FF", "id": "Angkutan Darat", "label": "Angkutan Darat", "shape": "dot", "title": "Angkutan Darat dan Angkutan melalui Saluran Pipa\nBahan Bakar: BBM, BBG", "x": 320, "y": 171}, {"color": "#FFECEA", "id": "Industri Logam Dasar", "label": "Industri Logam Dasar", "shape": "dot", "title": "Industri Logam Dasar\nBahan Baku Pendukung: Gas Alam", "x": -399, "y": -453}, {"color": "#EEEEFF", "id": "Alat Pemadam Kebakaran", "label": "Alat Pemadam Kebakaran", "shape": "dot", "title": "Alat Pemadam Kebakaran\nBahan Baku Pendukung: Gas Alam", "x": -100, "y": 396}, {"color": "#EEEEFF", "id": "Rekayasa Hujan", "label": "Rekayasa Hujan", "shape": "dot", "title": "Rekayasa Hujan\nBahan Baku Pendukung: Gas Alam", "x": -592, "y": -706}, {"color": "#EEEEFF", "id": "LPG", "label": "LPG", "shape": "dot", "title": "LPG\nBahan Baku: Gas Alam", "x": 171, "y": -293}, {"color": "#EEEEFF", "id": "Pemanas/Pendingin Rumah", "label": "Pemanas/Pendingin Rumah", "shape": "dot", "title": "Pemanas/Pendingin Rumah\nBahan Bakar: Gas Alam", "x": -271, "y": -879}, {"color": "#EEEEFF", "id": "Soft Drink", "label": "Soft Drink", "shape": "dot", "title": "Soft Drink\nBahan Baku Pendukung: Gas Alam", "x": -445, "y": -814}, {"color": "#EEEEFF", "id": "Laundry", "label": "Laundry", "shape": "dot", "title": "Laundry\nBahan Penudukung: Air", "x": -861, "y": 197}, {"color": "#EEEEFF", "id": "Aquadest", "label": "Aquadest", "shape": "dot", "title": "Aquadest\nBahan Baku: Air", "x": -272, "y": 649}, {"color": "#EEEEFF", "id": "Aquabidest", "label": "Aquabidest", "shape": "dot", "title": "Aquabidest\nBahan Baku: Air", "x": -459, "y": 171}, {"color": "#FFECEA", "id": "Industri Furnitur", "label": "Industri Furnitur", "shape": "dot", "title": "Industri Furnitur\nBahan Baku Pendukung: Air (Media Penangkap Cat)", "x": -449, "y": 12}]);
                  edges = new vis.DataSet([{"from": "Pertambangan Batu Bara", "label": "0.1", "to": "Batu Bara", "width": 2.0}, {"from": "Batu Bara", "label": "0.1", "to": "Industri Batu Bara \u0026 Minyak Bumi", "width": 2.0}, {"from": "Industri Batu Bara \u0026 Minyak Bumi", "label": "0.1", "to": "Briket", "width": 2.0}, {"from": "Batu Bara", "label": "0.1", "to": "Industri Bahan Kimia", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Pupuk", "width": 2.0}, {"from": "Batu Bara", "label": "0.1", "to": "Industri Barang Logam", "width": 2.0}, {"from": "Industri Barang Logam", "label": "0.1", "to": "Aluminium Composite Panel", "width": 2.0}, {"from": "Batu Bara", "label": "0.1", "to": "Industri Karet \u0026 Plastik", "width": 2.0}, {"from": "Industri Karet \u0026 Plastik", "label": "0.1", "to": "Isolasi Bangunan", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Cat", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Sampo", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Sabun", "width": 2.0}, {"from": "Batu Bara", "label": "0.1", "to": "Industri Tekstil", "width": 2.0}, {"from": "Industri Tekstil", "label": "0.1", "to": "Kain", "width": 2.0}, {"from": "Batu Bara", "label": "0.1", "to": "Pengadaan Listrik \u0026 Gas", "width": 2.0}, {"from": "Pengadaan Listrik \u0026 Gas", "label": "0.1", "to": "Listrik", "width": 2.0}, {"from": "Batu Bara", "label": "0.1", "to": "Coal Water Mixture", "width": 2.0}, {"from": "Pengadaan Listrik \u0026 Gas", "label": "0.1", "to": "Bahan Bakar Gas", "width": 2.0}, {"from": "Bahan Bakar Gas", "label": "0.1", "to": "Industri Barang Galian Bukan Logam", "width": 2.0}, {"from": "Industri Barang Galian Bukan Logam", "label": "0.1", "to": "Semen", "width": 2.0}, {"from": "Bahan Bakar Gas", "label": "0.1", "to": "Industri Bahan Kimia", "width": 2.0}, {"from": "Bahan Bakar Gas", "label": "0.1", "to": "Industri Barang Logam", "width": 2.0}, {"from": "Industri Barang Logam", "label": "0.1", "to": "Aluminium", "width": 2.0}, {"from": "Bahan Bakar Gas", "label": "0.1", "to": "Industri Kertas", "width": 2.0}, {"from": "Industri Kertas", "label": "0.1", "to": "Kertas", "width": 2.0}, {"from": "Pertambangan Minyak Bumi \u0026 Gas Alam", "label": "0.1", "to": "Minyak Bumi", "width": 2.0}, {"from": "Minyak Bumi", "label": "0.1", "to": "Industri Karet \u0026 Plastik", "width": 2.0}, {"from": "Industri Karet \u0026 Plastik", "label": "0.1", "to": "Alat Rumah Tangga dari Plastik", "width": 2.0}, {"from": "Minyak Bumi", "label": "0.1", "to": "Pengadaan Listrik \u0026 Gas", "width": 2.0}, {"from": "Minyak Bumi", "label": "0.1", "to": "Industri Batu Bara \u0026 Minyak Bumi", "width": 2.0}, {"from": "Industri Batu Bara \u0026 Minyak Bumi", "label": "0.1", "to": "Bahan Bakar Minyak", "width": 2.0}, {"from": "Minyak Bumi", "label": "0.1", "to": "Industri Bahan Kimia", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Pestisida", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Insektisida", "width": 2.0}, {"from": "Minyak Bumi", "label": "0.1", "to": "Industri Komputer \u0026 Elektronik", "width": 2.0}, {"from": "Industri Komputer \u0026 Elektronik", "label": "0.1", "to": "TV", "width": 2.0}, {"from": "Industri Komputer \u0026 Elektronik", "label": "0.1", "to": "Ponsel", "width": 2.0}, {"from": "Industri Komputer \u0026 Elektronik", "label": "0.1", "to": "Komputer", "width": 2.0}, {"from": "Industri Batu Bara \u0026 Minyak Bumi", "label": "0.1", "to": "Atap Aspal (Bitumen)", "width": 2.0}, {"from": "Industri Karet \u0026 Plastik", "label": "0.1", "to": "Pipa", "width": 2.0}, {"from": "Industri Batu Bara \u0026 Minyak Bumi", "label": "0.1", "to": "Aspal", "width": 2.0}, {"from": "Minyak Bumi", "label": "0.1", "to": "Industri Tekstil", "width": 2.0}, {"from": "Industri Tekstil", "label": "0.1", "to": "Polyester", "width": 2.0}, {"from": "Industri Tekstil", "label": "0.1", "to": "Nilon", "width": 2.0}, {"from": "Minyak Bumi", "label": "0.1", "to": "Industri Farmasi \u0026 Obat", "width": 2.0}, {"from": "Industri Farmasi \u0026 Obat", "label": "0.1", "to": "Produk Pembersih", "width": 2.0}, {"from": "Industri Farmasi \u0026 Obat", "label": "0.1", "to": "Obat", "width": 2.0}, {"from": "Industri Farmasi \u0026 Obat", "label": "0.1", "to": "Alat Pelindung", "width": 2.0}, {"from": "Industri Farmasi \u0026 Obat", "label": "0.1", "to": "Alat Keamanan Tenaga Medis", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Parfum", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Cat Kuku", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Make Up", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Pewarna Rambut", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Pasta Gigi", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Pengadaan Listrik \u0026 Gas", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Industri Makanan", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Pertanian \u0026 Peternakan", "width": 2.0}, {"from": "Pertanian \u0026 Peternakan", "label": "0.1", "to": "Jamur", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Industri Tekstil", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Industri Kertas", "width": 2.0}, {"from": "Industri Kertas", "label": "0.1", "to": "Pulp", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Industri Kayu \u0026 Gabus", "width": 2.0}, {"from": "Industri Kayu \u0026 Gabus", "label": "0.1", "to": "Kayu", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Konstruksi", "width": 2.0}, {"from": "Konstruksi", "label": "0.1", "to": "Blok Beton", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Industri Barang Galian Bukan Logam", "width": 2.0}, {"from": "Industri Makanan", "label": "0.1", "to": "Daging", "width": 2.0}, {"from": "Industri Makanan", "label": "0.1", "to": "Gula", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Industri Bahan Kimia", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Etanol", "width": 2.0}, {"from": "Etanol", "label": "0.1", "to": "Industri Batu Bara \u0026 Minyak Bumi", "width": 2.0}, {"from": "Industri Batu Bara \u0026 Minyak Bumi", "label": "0.1", "to": "Biofuel", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Treatment Limbah \u0026 Sampah", "width": 2.0}, {"from": "Treatment Limbah \u0026 Sampah", "label": "0.1", "to": "Biogas", "width": 2.0}, {"from": "Industri Makanan", "label": "0.1", "to": "Buah dan Sayur Kering", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Aktivitas Olahraga \u0026 Rekreasi", "width": 2.0}, {"from": "Aktivitas Olahraga \u0026 Rekreasi", "label": "0.1", "to": "Pemandian Air Panas", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Industri Mesin", "width": 2.0}, {"from": "Industri Mesin", "label": "0.1", "to": "Pompa Panas Geothermal", "width": 2.0}, {"from": "Panas Bumi", "label": "0.1", "to": "Penyediaan Makanan \u0026 Minuman", "width": 2.0}, {"from": "Penyediaan Makanan \u0026 Minuman", "label": "0.1", "to": "Restoran", "width": 2.0}, {"from": "Pertambangan Bijih Logam", "label": "0.1", "to": "Uranium \u0026 Plutonium (Nuklir)", "width": 2.0}, {"from": "Uranium \u0026 Plutonium (Nuklir)", "label": "0.1", "to": "Pengadaan Listrik \u0026 Gas", "width": 2.0}, {"from": "Uranium \u0026 Plutonium (Nuklir)", "label": "0.1", "to": "Industri Farmasi \u0026 Obat", "width": 2.0}, {"from": "Industri Farmasi \u0026 Obat", "label": "0.1", "to": "Pengobatan Kanker", "width": 2.0}, {"from": "Uranium \u0026 Plutonium (Nuklir)", "label": "0.1", "to": "Penelitian", "width": 2.0}, {"from": "Uranium \u0026 Plutonium (Nuklir)", "label": "0.1", "to": "Treatment Air", "width": 2.0}, {"from": "Treatment Air", "label": "0.1", "to": "Air Bersih", "width": 2.0}, {"from": "Uranium \u0026 Plutonium (Nuklir)", "label": "0.1", "to": "Industri Makanan", "width": 2.0}, {"from": "Uranium \u0026 Plutonium (Nuklir)", "label": "0.1", "to": "Administrasi Pemerintahan", "width": 2.0}, {"from": "Administrasi Pemerintahan", "label": "0.1", "to": "Pertahanan", "width": 2.0}, {"from": "Pertambangan Minyak Bumi \u0026 Gas Alam", "label": "0.1", "to": "Gas Alam", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Pengadaan Listrik \u0026 Gas", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Bahan Bakar Gas", "width": 2.0}, {"from": "Bahan Bakar Gas", "label": "0.1", "to": "Angkutan Darat", "width": 2.0}, {"from": "Bahan Bakar Minyak", "label": "0.1", "to": "Angkutan Darat", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Industri Karet \u0026 Plastik", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Industri Logam Dasar", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Industri Bahan Kimia", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Alat Pemadam Kebakaran", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Pengelolaan Kehutanan", "width": 2.0}, {"from": "Pengelolaan Kehutanan", "label": "0.1", "to": "Rekayasa Hujan", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Penelitian", "width": 2.0}, {"from": "Pengadaan Listrik \u0026 Gas", "label": "0.1", "to": "LPG", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Konstruksi Khusus", "width": 2.0}, {"from": "Konstruksi Khusus", "label": "0.1", "to": "Pemanas/Pendingin Rumah", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Dry Ice", "width": 2.0}, {"from": "Dry Ice", "label": "0.1", "to": "Industri Makanan", "width": 2.0}, {"from": "Gas Alam", "label": "0.1", "to": "Industri Minuman", "width": 2.0}, {"from": "Industri Minuman", "label": "0.1", "to": "Soft Drink", "width": 2.0}, {"from": "Treatment Air", "label": "0.1", "to": "Air", "width": 2.0}, {"from": "Air", "label": "0.1", "to": "Pengadaan Listrik \u0026 Gas", "width": 2.0}, {"from": "Air", "label": "0.1", "to": "Aktivitas Jasa Perorangan", "width": 2.0}, {"from": "Aktivitas Jasa Perorangan", "label": "0.1", "to": "Laundry", "width": 2.0}, {"from": "Air", "label": "0.1", "to": "Industri Bahan Kimia", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Aquadest", "width": 2.0}, {"from": "Industri Bahan Kimia", "label": "0.1", "to": "Aquabidest", "width": 2.0}, {"from": "Air", "label": "0.1", "to": "Industri Furnitur", "width": 2.0}]);

                  nodeColors = {};
                  allNodes = nodes.get({ returnType: "Object" });
                  for (nodeId in allNodes) {
                    nodeColors[nodeId] = allNodes[nodeId].color;
                  }
                  allEdges = edges.get({ returnType: "Object" });
                  // adding nodes and edges to the graph
                  data = {nodes: nodes, edges: edges};

                  var options = {"edges": {"arrows": {"to": {"enabled": true}}, "color": {"inherit": true}, "selfReferenceSize": null, "selfReference": {"angle": 0.7853981633974483}, "smooth": {"forceDirection": "none"}}, "physics": {"forceAtlas2Based": {"springLength": 100}, "minVelocity": 0.75, "solver": "forceAtlas2Based"}};

                  


                  

                  network = new vis.Network(container, data, options);

                  

                  
                    network.on("selectNode", neighbourhoodHighlight);
                  

                  


                  

                  return network;

              }
              drawGraph();
        </script>
    </body>
</html>