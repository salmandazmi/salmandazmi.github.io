---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-ecosystem-industry
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
                 height: 1080px;
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
                                
                                    <option value="_K03 - Jasa Keuangan Lainnya">_K03 - Jasa Keuangan Lainnya</option>
                                
                                    <option value="_K04 - Jasa Penunjang Keuangan">_K04 - Jasa Penunjang Keuangan</option>
                                
                                    <option value="_L - Real Estate">_L - Real Estate</option>
                                
                                    <option value="_M - Jasa Perusahaan">_M - Jasa Perusahaan</option>
                                
                                    <option value="_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib">_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib</option>
                                
                                    <option value="_P - Jasa Pendidikan">_P - Jasa Pendidikan</option>
                                
                                    <option value="_Q - Jasa Kesehatan dan Kegiatan Sosial">_Q - Jasa Kesehatan dan Kegiatan Sosial</option>
                                
                                    <option value="_R - Jasa lainnya">_R - Jasa lainnya</option>
                                
                                    <option value="Resources">Resources</option>
                                
                                    <option value="Manufactures">Manufactures</option>
                                
                                    <option value="Infrastructures">Infrastructures</option>
                                
                                    <option value="Products">Products</option>
                                
                            </select>
                        </div>
                        <div class="col-2 pb-2">
                            <button type="button" class="btn btn-primary btn-block" onclick="neighbourhoodHighlight({nodes: []});">Reset Selection</button>
                        </div>
                    </div>
                </div>
            
            
              <div id="filter-menu" class="card-header">
                <div class="row no-gutters">
                  <div class="col-3 pb-2">
                    <select
                            class="form-select"
                            aria-label="Default select example"
                            onchange="updateFilter(value, 'item')"
                            id="select-item"
                        >
                        <option value="">Select a network item</option>
                        <option value="edge">edge</option>
                        <option value="node">node</option>
                    </select>
                  </div>
                  <div class="col-3 pb-2">
                    <select
                            class="form-select"
                            aria-label="Default select example"
                            onchange="updateFilter(value, 'property')"
                            id="select-property"
                        >
                        <option value="">Select a property...</option>
                    </select>
                  </div>
                  <div class="col-3 pb-2">
                    <select
                            class="form-select"
                            aria-label="Default select example"
                            id="select-value"
                        >
                        <option value="">Select value(s)...</option>
                    </select>
                  </div>
                  <div class="col-1 pb-2">
                    <button type="button" class="btn btn-primary btn-block" onclick="highlightFilter(filter);">Filter</button>
                  </div>
                  <div class="col-2 pb-2">
                    <button type="button" class="btn btn-primary btn-block" onclick="clearFilter(true)">Reset Selection</button>
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
              

              
                  // explicitly using onItemAdd and this function as we need to save multiple values
                  let updateValueFilter = function() {
                      return function () {
                      filter['value'].push(arguments[0])
                      }
                  }

                  let valueControl = new TomSelect("#select-value",{
                      maxItems: null,
                      valueField: 'id',
                      labelField: 'title',
                      searchField: 'title',
                      create: false,
                      sortField: {
                          field: "text",
                          direction: "asc"
                      },
                      onItemAdd: updateValueFilter()
                  });

                  let addValues = function() {
                      return function () {
                          // clear the current value options and add the selected attribute values
                          // tom-select handles duplicates
                          let selectedProperty = arguments[0];
                          valueControl.clear();
                          valueControl.clearOptions();
                          filter['value'] = []
                          if (filter['item'] === 'node') {
                              for (let each in allNodes) {
                                  valueControl.addOption({
                                      id:allNodes[each][selectedProperty],
                                      title:allNodes[each][selectedProperty]
                                  })
                              }
                          }
                          else if (filter['item'] === 'edge') {
                              for (let each in allEdges) {
                                  valueControl.addOption({
                                      id:allEdges[each][selectedProperty],
                                      title:allEdges[each][selectedProperty]
                                  })
                              }
                          }
                      }
                  };

                  let propControl = new TomSelect("#select-property",{
                      valueField: 'id',
                      labelField: 'title',
                      searchField: 'title',
                      create: false,
                      sortField: {
                          field: "text",
                          direction: "asc"
                      },
                      onItemAdd: addValues()
                  });

                  let addProperties = function() {
                      return function () {
                          // loops through the selected network item and adds the attributes to dropdown
                          // tom-select handles duplicates
                          clearFilter(false)
                          if (arguments[0] === 'edge') {
                              for (let each in allEdges) {
                                  if (allEdges.hasOwnProperty(each)) {
                                      for (let eachProp in allEdges[each]) {
                                          if (allEdges[each].hasOwnProperty(eachProp)) {
                                              propControl.addOption({id: eachProp, title: eachProp})
                                          }
                                      }
                                  }
                              }
                          }
                          else if (arguments[0] === 'node') {
                              for (let each in allNodes) {
                                  if (allNodes.hasOwnProperty(each)) {
                                      for (let eachProp in allNodes[each]) {
                                          if (allNodes[each].hasOwnProperty(eachProp)
                                              && (eachProp !== 'hidden' && eachProp !== 'savedLabel'
                                                  && eachProp !== 'hiddenLabel')) {
                                              propControl.addOption({id: eachProp, title: eachProp})

                                          }
                                      }
                                  }
                              }
                          }
                      }
                  };

                  let itemControl = new TomSelect("#select-item",{
                      create: false,
                      sortField:{
                          field: "text",
                          direction: "asc"
                      },
                      onItemAdd: addProperties()
                  });

                  function clearFilter(reset) {
                      // utility function to clear all the selected filter options
                      // if reset is set to true, the existing filter will be removed
                      // else, only the dropdown options are cleared
                      propControl.clear();
                      propControl.clearOptions();
                      valueControl.clear();
                      valueControl.clearOptions();
                      filter = {
                          item : '',
                          property : '',
                          value : []
                      }
                      if (reset) {
                          itemControl.clear();
                          filterHighlight({nodes: []})
                      }
                  }

                  function updateFilter(value, key) {
                      // key could be 'item' or 'property' and value is as selected in dropdown
                      filter[key] = value
                  }

              

              // This method is responsible for drawing the graph, returns the drawn network
              function drawGraph() {
                  var container = document.getElementById('mynetwork');

                  

                  // parsing and collecting nodes and edges from the python
                  nodes = new vis.DataSet([{"color": "#F8AD3C", "id": "_A01a - Tanaman Pangan", "label": "Tanaman Pangan", "physics": false, "shape": "dot", "size": 13.627925402211554, "title": "Tanaman Pangan\n\nDependent on Demand\nBackward: 0,76 | Forward: 1,25", "x": -535.5787083219975, "y": -247.4553598442466}, {"color": "#F05D5E", "id": "_A01b - Tanaman Hortikultura", "label": "Tanaman Hortikultura", "physics": false, "shape": "dot", "size": 12.205152636035017, "title": "Tanaman Hortikultura\n\nIndependent\nBackward: 0,75 | Forward: 0,75", "x": -603.8537537405335, "y": -227.40800818061203}, {"color": "#F8AD3C", "id": "_A01c - Tanaman Perkebunan", "label": "Tanaman Perkebunan", "physics": false, "shape": "dot", "size": 16.24068386605136, "title": "Tanaman Perkebunan\n\nDependent on Demand\nBackward: 0,79 | Forward: 1,17", "x": -663.7151677128533, "y": -188.9374122097825}, {"color": "#F05D5E", "id": "_A01d - Peternakan", "label": "Peternakan", "physics": false, "shape": "dot", "size": 12.503437124845707, "title": "Peternakan\n\nIndependent\nBackward: 0,96 | Forward: 0,98", "x": -710.3133785174289, "y": -135.160194490491}, {"color": "#F8AD3C", "id": "_A01e - Jasa Pertanian dan Perburuan", "label": "Jasa Pertanian \u0026 Perburuan", "physics": false, "shape": "dot", "size": 10.288043882379306, "title": "Jasa Pertanian dan Perburuan\n\nDependent on Demand\nBackward: 0,82 | Forward: 1,66", "x": -739.8732440312525, "y": -70.4331490702102}, {"color": "#F8AD3C", "id": "_A02 - Kehutanan dan Penebangan Kayu", "label": "Kehutanan \u0026 Penebangan \nKayu", "physics": false, "shape": "dot", "size": 11.00088024241666, "title": "Kehutanan dan Penebangan Kayu\n\nDependent on Demand\nBackward: 0,73 | Forward: 1,36", "x": -749.999998645349, "y": 1.9846265183502144e-05}, {"color": "#F05D5E", "id": "_A03 - Perikanan", "label": "Perikanan", "physics": false, "shape": "dot", "size": 14.269479818525962, "title": "Perikanan\n\nIndependent\nBackward: 0,75 | Forward: 0,83", "x": -739.8732440312525, "y": 70.43313760077184}, {"color": "#F8AD3C", "id": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "label": "Pertambangan Minyak, Gas \n\u0026 Panas Bumi", "physics": false, "shape": "dot", "size": 14.008167049652032, "title": "Pertambangan Minyak, Gas dan Panas Bumi\n\nDependent on Demand\nBackward: 0,84 | Forward: 1,51", "x": -710.3134083197513, "y": 135.16017557047206}, {"color": "#F8AD3C", "id": "_B02 - Pertambangan Batubara dan Lignit", "label": "Pertambangan Batubara \n\u0026 Lignit", "physics": false, "shape": "dot", "size": 18.588871151279136, "title": "Pertambangan Batubara dan Lignit\n\nDependent on Demand\nBackward: 0,99 | Forward: 1,03", "x": -663.7151826140145, "y": 188.93739328976355}, {"color": "#F8AD3C", "id": "_B03 - Pertambangan Bijih Logam", "label": "Pertambangan Bijih Logam", "physics": false, "shape": "dot", "size": 12.092443658587992, "title": "Pertambangan Bijih Logam\n\nDependent on Demand\nBackward: 0,87 | Forward: 1,16", "x": -603.8537760922752, "y": 227.40798926059307}, {"color": "#F8AD3C", "id": "_B04 - Pertambangan dan Penggalian Lainnya", "label": "Pertambangan \u0026 Penggalian \nLainnya", "physics": false, "shape": "dot", "size": 12.21808473814006, "title": "Pertambangan dan Penggalian Lainnya\n\nDependent on Demand\nBackward: 0,88 | Forward: 1,27", "x": -535.578753025481, "y": 247.4553558253888}, {"color": "#F8AD3C", "id": "_C01 - Industri Batubara dan Pengilangan Migas", "label": "Industri Batubara \n\u0026 Pengilangan Migas", "physics": false, "shape": "dot", "size": 13.0803102617343, "title": "Industri Batubara dan Pengilangan Migas\n\nDependent on Demand\nBackward: 0,93 | Forward: 1,18", "x": 249.9999999999999, "y": 1.4279868317292898e-06}, {"color": "#F8AD3C", "id": "_C02 - Industri Makanan dan Minuman", "label": "Industri Makanan \u0026 Minuman", "physics": false, "shape": "dot", "size": 20.526201891149423, "title": "Industri Makanan dan Minuman\n\nDependent on Supply\nBackward: 1,17| Forward: 0,82", "x": 230.96987615964807, "y": -95.67086201156155}, {"color": "#F05D5E", "id": "_C03 - Industri Pengolahan Tembakau", "label": "Industri Pengolahan Tembakau", "physics": false, "shape": "dot", "size": 11.137105769087773, "title": "Industri Pengolahan Tembakau\n\nIndependent\nBackward: 0,83| Forward: 0,58", "x": 176.7766926894802, "y": -176.77668983350662}, {"color": "#F8AD3C", "id": "_C04 - Industri Tekstil dan Pakaian Jadi", "label": "Industri Tekstil \u0026 Pakaian Jadi", "physics": false, "shape": "dot", "size": 11.568818905955855, "title": "Industri Tekstil dan Pakaian Jadi\n\nDependent on Supply\nBackward: 1,06| Forward: 0,84", "x": 95.67086486753513, "y": -230.9698733036745}, {"color": "#F8AD3C", "id": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "label": "Industri Kulit, Barang dari Kulit \n\u0026 Alas Kaki", "physics": false, "shape": "dot", "size": 10.378770901590794, "title": "Industri Kulit, Barang dari Kulit dan Alas Kaki\n\nDependent on Supply\nBackward: 1,09| Forward: 0,71", "x": -9.499860260802633e-06, "y": -249.99999714402634}, {"color": "#00A39D", "id": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "label": "Industri Kayu, Gabus \n\u0026 Anyaman Bambu", "physics": false, "shape": "dot", "size": 10.630612282573969, "title": "Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya\n\nDependent\nBackward: 1,11| Forward: 1,05", "x": -95.67084711040053, "y": -230.9698882048356}, {"color": "#00A39D", "id": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "label": "Industri Kertas, Percetakan \n\u0026 Media Rekaman", "physics": false, "shape": "dot", "size": 11.084670448828762, "title": "Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman\n\nDependent\nBackward: 1,16 | Forward: 1,19", "x": -176.7766898335067, "y": -176.77668983350662}, {"color": "#00A39D", "id": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "label": "Industri Kimia, Farmasi \n\u0026 Obat Tradisional", "physics": false, "shape": "dot", "size": 12.817799819592611, "title": "Industri Kimia, Farmasi dan Obat Tradisional\n\nDependent\nBackward: 1,06 | Forward: 1,09", "x": -230.96987330367458, "y": -95.67086946214211}, {"color": "#00A39D", "id": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "label": "Industri Karet \u0026 Plastik", "physics": false, "shape": "dot", "size": 10.645098975349622, "title": "Industri Karet, Barang dari Karet dan Plastik\n\nDependent\nBackward: 1,20| Forward: 1,04", "x": -249.99999714402642, "y": 2.328368085025968e-05}, {"color": "#00A39D", "id": "_C10 - Industri Barang Galian bukan Logam", "label": "Industri Barang Galian \nbukan Logam", "physics": false, "shape": "dot", "size": 10.75680489181967, "title": "Industri Barang Galian bukan Logam\n\nDependent\nBackward: 1,17 | Forward: 1,17", "x": -230.96987330367458, "y": 95.67086486753522}, {"color": "#F8AD3C", "id": "_C11 - Industri Logam Dasar", "label": "Industri Logam Dasar", "physics": false, "shape": "dot", "size": 11.510092916471585, "title": "Industri Logam Dasar\n\nDependent on Supply\nBackward: 1,14| Forward: 0,97", "x": -176.77670473466782, "y": 176.77667778831918}, {"color": "#F8AD3C", "id": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "label": "Industri Barang Logam, \nElektronik \u0026 Optik", "physics": false, "shape": "dot", "size": 12.522936814850636, "title": "Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik\n\nDependent on Supply\nBackward: 1,01| Forward: 0,89", "x": -95.67089181388386, "y": 230.96986125848704}, {"color": "#F8AD3C", "id": "_C13 - Industri Mesin dan Perlengkapan", "label": "Industri Mesin \n\u0026 Perlengkapan", "physics": false, "shape": "dot", "size": 10.434524630634078, "title": "Industri Mesin dan Perlengkapan\n\nDependent on Supply\nBackward: 1,01| Forward: 0,73", "x": 4.4092068910597545e-06, "y": 250.0}, {"color": "#F05D5E", "id": "_C14 - Industri Alat Angkutan", "label": "Industri Alat Angkutan", "physics": false, "shape": "dot", "size": 12.391442751422925, "title": "Industri Alat Angkutan\n\nIndependent\nBackward: 0,99| Forward: 0,81", "x": 95.67089466985735, "y": 230.96986125848704}, {"color": "#F8AD3C", "id": "_C15 - Industri Furnitur", "label": "Industri Furnitur", "physics": false, "shape": "dot", "size": 10.324488687615736, "title": "Industri Furnitur\n\nDependent on Supply\nBackward: 1,10| Forward: 0,62", "x": 176.77666288715798, "y": 176.7767224918025}, {"color": "#F05D5E", "id": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "label": "Industri Pengolahan Lainnya", "physics": false, "shape": "dot", "size": 10.190314666792476, "title": "Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan\n\nIndependent\nBackward: 0,96| Forward: 0,73", "x": 230.96989106080918, "y": 95.67085741695466}, {"color": "#00A39D", "id": "_D01 - Ketenagalistrikan", "label": "Ketenagalistrikan", "physics": false, "shape": "dot", "size": 11.418900520043014, "title": "Ketenagalistrikan\n\nDependent\nBackward: 1,89| Forward: 1,68", "x": 433.0126921387436, "y": -499.9999943810616}, {"color": "#F8AD3C", "id": "_D02 - Pengadaan Gas dan Produksi Es", "label": "Pengadaan Gas \n\u0026 Produksi Es", "physics": false, "shape": "dot", "size": 10.259925867845178, "title": "Pengadaan Gas dan Produksi Es\n\nDependent on Demand\nBackward: 0,91 | Forward: 1,85", "x": 396.67665787460845, "y": -554.3807088669869}, {"color": "#F8AD3C", "id": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "label": "Pengadaan Air \n\u0026 Pengelolaan Limbah", "physics": false, "shape": "dot", "size": 10.102192990778095, "title": "Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang\n\nDependent on Supply\nBackward: 1,01| Forward: 0,83", "x": 353.5533831561043, "y": -603.5533781477134}, {"color": "#F8AD3C", "id": "_F - Konstruksi", "label": "Konstruksi", "physics": false, "shape": "dot", "size": 25.94116965320039, "title": "Konstruksi\n\nDependent on Supply\nBackward: 1,13| Forward: 0,60", "x": 304.38071387537775, "y": -646.6766528662175}, {"color": "#F05D5E", "id": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "label": "Perdagangan Mobil \u0026 Motor", "physics": false, "shape": "dot", "size": 13.593672869815915, "title": "Perdagangan Mobil, Sepeda Motor dan Reparasinya\n\nIndependent\nBackward: 0,88 | Forward: 0,93", "x": 543.4120588986269, "y": -246.20193577067386}, {"color": "#F05D5E", "id": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "label": "Perdagangan Besar \u0026 Eceran, \nBukan Mobil \u0026 Motor", "physics": false, "shape": "dot", "size": 27.194042043434184, "title": "Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor\n\nIndependent\nBackward: 0,89| Forward: 0,87", "x": 624.9999944120647, "y": -216.50636574426974}, {"color": "#F8AD3C", "id": "_H01 - Angkutan Rel", "label": "Angkutan Rel", "physics": false, "shape": "dot", "size": 10.144270014169006, "title": "Angkutan Rel\n\nDependent on Supply\nBackward: 1,21| Forward: 1,00", "x": 249.99998448829146, "y": -683.0127169326747}, {"color": "#F8AD3C", "id": "_H02 - Angkutan Darat", "label": "Angkutan Darat", "physics": false, "shape": "dot", "size": 14.344232476304223, "title": "Angkutan Darat\n\nDependent on Supply\nBackward: 1,04| Forward: 0,97", "x": 191.34172780094613, "y": -711.9397448951246}, {"color": "#00A39D", "id": "_H03 - Angkutan Laut", "label": "Angkutan Laut", "physics": false, "shape": "dot", "size": 10.5286715954772, "title": "Angkutan Laut\n\nDependent\nBackward: 1,18| Forward: 1,02", "x": 129.40953701222858, "y": -732.9628989963489}, {"color": "#00A39D", "id": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "label": "Angkutan Sungai Danau", "physics": false, "shape": "dot", "size": 10.262514288234392, "title": "Angkutan Sungai Danau dan Penyeberangan\n\nDependent\nBackward: 1,11 | Forward: 1,15", "x": 65.26312309366124, "y": -745.7224056031788}, {"color": "#F8AD3C", "id": "_H05 - Angkutan Udara", "label": "Angkutan Udara", "physics": false, "shape": "dot", "size": 12.22241774617942, "title": "Angkutan Udara\n\nDependent on Supply\nBackward: 1,12| Forward: 0,78", "x": -2.0593261959801342e-05, "y": -749.9999925080822}, {"color": "#00A39D", "id": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "label": "Pergudangan \n\u0026 Jasa Angkutan", "physics": false, "shape": "dot", "size": 11.968834854420264, "title": "Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir\n\nDependent\nBackward: 1,06 | Forward: 1,15", "x": -65.26310566763611, "y": -745.722435405501}, {"color": "#F05D5E", "id": "_I01 - Penyediaan Akomodasi", "label": "Penyediaan Akomodasi", "physics": false, "shape": "dot", "size": 10.926628345908346, "title": "Penyediaan Akomodasi\n\nIndependent\nBackward: 0,96| Forward: 0,79", "x": 691.5111103428735, "y": -160.69689854496605}, {"color": "#F8AD3C", "id": "_I02 - Penyediaan Makan Minum", "label": "Penyediaan Makan Minum", "physics": false, "shape": "dot", "size": 13.121488068651217, "title": "Penyediaan Makan Minum\n\nDependent on Supply\nBackward: 1,13| Forward: 0,65", "x": 734.9231543403394, "y": -85.50503678422847}, {"color": "#F8AD3C", "id": "_J - Informasi dan Komunikasi", "label": "Informasi \u0026 Komunikasi", "physics": false, "shape": "dot", "size": 16.797099635851943, "title": "Informasi dan Komunikasi\n\nDependent on Demand\nBackward: 0,98 | Forward: 1,07", "x": -129.4095046850424, "y": -732.9628989963489}, {"color": "#F8AD3C", "id": "_K01 - Jasa Perantara Keuangan", "label": "Jasa Perantara Keuangan", "physics": false, "shape": "dot", "size": 14.297483988716078, "title": "Jasa Perantara Keuangan\n\nDependent on Demand\nBackward: 0,84 | Forward: 1,20", "x": -191.34169547375996, "y": -711.9397746974468}, {"color": "#F05D5E", "id": "_K02 - Asuransi dan Dana Pensiun", "label": "Asuransi \u0026 Dana Pensiun", "physics": false, "shape": "dot", "size": 11.331992670885745, "title": "Asuransi dan Dana Pensiun\n\nIndependent\nBackward: 0,88| Forward: 0,83", "x": -250.00002666691066, "y": -683.0126871303526}, {"color": "#F8AD3C", "id": "_K03 - Jasa Keuangan Lainnya", "label": "Jasa Keuangan Lainnya", "physics": false, "shape": "dot", "size": 10.93266824988936, "title": "Jasa Keuangan Lainnya\n\nDependent on Demand\nBackward: 0,92 | Forward: 1,36", "x": -304.38074115283587, "y": -646.6766528662175}, {"color": "#F8AD3C", "id": "_K04 - Jasa Penunjang Keuangan", "label": "Jasa Penunjang Keuangan", "physics": false, "shape": "dot", "size": 10.123654957280166, "title": "Jasa Penunjang Keuangan\n\nDependent on Demand\nBackward: 0,89 | Forward: 1,35", "x": -353.55338063124026, "y": -603.5533781477134}, {"color": "#F05D5E", "id": "_L - Real Estate", "label": "Real Estate", "physics": false, "shape": "dot", "size": 13.888072035777892, "title": "Real Estate\n\nIndependent\nBackward: 0,84| Forward: 0,76", "x": 750.0, "y": -7.0091011455730624e-06}, {"color": "#F8AD3C", "id": "_M - Jasa Perusahaan", "label": "Jasa Perusahaan", "physics": false, "shape": "dot", "size": 12.946813922445335, "title": "Jasa Perusahaan\n\nDependent on Demand\nBackward: 0,98 | Forward: 1,23", "x": 734.9231394391784, "y": 85.50508237067007}, {"color": "#F8AD3C", "id": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "label": "Administrasi Pemerintahan \n\u0026 Jaminan Sosial", "physics": false, "shape": "dot", "size": 14.741804617465055, "title": "Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib\n\nDependent on Supply\nBackward: 1,05| Forward: 0,62", "x": -396.6766553497444, "y": -554.380738669309}, {"color": "#F05D5E", "id": "_P - Jasa Pendidikan", "label": "Jasa Pendidikan", "physics": false, "shape": "dot", "size": 14.489254786205962, "title": "Jasa Pendidikan\n\nIndependent\nBackward: 0,94| Forward: 0,58", "x": 691.5110805405516, "y": 160.69692923024667}, {"color": "#F8AD3C", "id": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "label": "Jasa Kesehatan \u0026 Kegiatan Sosial", "physics": false, "shape": "dot", "size": 11.938419184105278, "title": "Jasa Kesehatan dan Kegiatan Sosial\n\nDependent on Supply\nBackward: 1,08| Forward: 0,62", "x": 624.9999795109037, "y": 216.50635172606746}, {"color": "#F05D5E", "id": "_R - Jasa lainnya", "label": "Jasa lainnya", "physics": false, "shape": "dot", "size": 13.116808143050033, "title": "Jasa lainnya\n\nIndependent\nBackward: 0,96| Forward: 0,72", "x": 543.412043997466, "y": 246.20192175247158}, {"color": "#2D2A32", "font": {"size": 30}, "id": "Resources", "label": "Resources", "physics": false, "shape": "dot", "size": 0.0, "title": "Resources", "x": -925.0, "y": -30.0}, {"color": "#2D2A32", "font": {"size": 30}, "id": "Manufactures", "label": "Manufactures", "physics": false, "shape": "dot", "size": 0.0, "title": "Manufactures", "x": 0.0, "y": -30.0}, {"color": "#2D2A32", "font": {"size": 30}, "id": "Infrastructures", "label": "Infrastructures", "physics": false, "shape": "dot", "size": 0.0, "title": "Infrastructures", "x": 0.0, "y": -875.0}, {"color": "#2D2A32", "font": {"size": 30}, "id": "Products", "label": "Products", "physics": false, "shape": "dot", "size": 0.0, "title": "Products", "x": 900.0, "y": -30.0}]);
                  edges = new vis.DataSet([{"color": "#E8E8E8", "from": "_A01a - Tanaman Pangan", "to": "_A01a - Tanaman Pangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01a - Tanaman Pangan", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01a - Tanaman Pangan", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01a - Tanaman Pangan", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01b - Tanaman Hortikultura", "to": "_A01b - Tanaman Hortikultura", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01b - Tanaman Hortikultura", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01b - Tanaman Hortikultura", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01b - Tanaman Hortikultura", "to": "_I01 - Penyediaan Akomodasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01b - Tanaman Hortikultura", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01b - Tanaman Hortikultura", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_A01c - Tanaman Perkebunan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_C03 - Industri Pengolahan Tembakau", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01c - Tanaman Perkebunan", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01d - Peternakan", "to": "_A01d - Peternakan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01d - Peternakan", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01d - Peternakan", "to": "_I01 - Penyediaan Akomodasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01d - Peternakan", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01d - Peternakan", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01e - Jasa Pertanian dan Perburuan", "to": "_A01a - Tanaman Pangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01e - Jasa Pertanian dan Perburuan", "to": "_A01b - Tanaman Hortikultura", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01e - Jasa Pertanian dan Perburuan", "to": "_A01c - Tanaman Perkebunan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A01e - Jasa Pertanian dan Perburuan", "to": "_A01d - Peternakan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_A02 - Kehutanan dan Penebangan Kayu", "width": 2.0}, {"color": "#E8E8E8", "from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "width": 2.0}, {"color": "#E8E8E8", "from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_A02 - Kehutanan dan Penebangan Kayu", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_A03 - Perikanan", "to": "_A03 - Perikanan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A03 - Perikanan", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_A03 - Perikanan", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_A03 - Perikanan", "to": "_I01 - Penyediaan Akomodasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_A03 - Perikanan", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_A03 - Perikanan", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_A03 - Perikanan", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_C01 - Industri Batubara dan Pengilangan Migas", "width": 2.0}, {"color": "#E8E8E8", "from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "to": "_D02 - Pengadaan Gas dan Produksi Es", "width": 2.0}, {"color": "#E8E8E8", "from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "width": 2.0}, {"color": "#E8E8E8", "from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_B02 - Pertambangan Batubara dan Lignit", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_B03 - Pertambangan Bijih Logam", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_B03 - Pertambangan Bijih Logam", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_B04 - Pertambangan dan Penggalian Lainnya", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_A03 - Perikanan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_C01 - Industri Batubara dan Pengilangan Migas", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_D02 - Pengadaan Gas dan Produksi Es", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_C01 - Industri Batubara dan Pengilangan Migas", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_I01 - Penyediaan Akomodasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_C02 - Industri Makanan dan Minuman", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C03 - Industri Pengolahan Tembakau", "to": "_C03 - Industri Pengolahan Tembakau", "width": 2.0}, {"color": "#E8E8E8", "from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_C04 - Industri Tekstil dan Pakaian Jadi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_C04 - Industri Tekstil dan Pakaian Jadi", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_C03 - Industri Pengolahan Tembakau", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_J - Informasi dan Komunikasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_K01 - Jasa Perantara Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_K03 - Jasa Keuangan Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_A01a - Tanaman Pangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C04 - Industri Tekstil dan Pakaian Jadi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_J - Informasi dan Komunikasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_K01 - Jasa Perantara Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C10 - Industri Barang Galian bukan Logam", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_C10 - Industri Barang Galian bukan Logam", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_C10 - Industri Barang Galian bukan Logam", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C10 - Industri Barang Galian bukan Logam", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C10 - Industri Barang Galian bukan Logam", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C11 - Industri Logam Dasar", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_C11 - Industri Logam Dasar", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_C11 - Industri Logam Dasar", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C11 - Industri Logam Dasar", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C11 - Industri Logam Dasar", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C11 - Industri Logam Dasar", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C11 - Industri Logam Dasar", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C13 - Industri Mesin dan Perlengkapan", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_C14 - Industri Alat Angkutan", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C15 - Industri Furnitur", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_C15 - Industri Furnitur", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C04 - Industri Tekstil dan Pakaian Jadi", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_I01 - Penyediaan Akomodasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_J - Informasi dan Komunikasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_L - Real Estate", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_D01 - Ketenagalistrikan", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_A03 - Perikanan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_D02 - Pengadaan Gas dan Produksi Es", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_D02 - Pengadaan Gas dan Produksi Es", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_A01c - Tanaman Perkebunan", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_K01 - Jasa Perantara Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_L - Real Estate", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_F - Konstruksi", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_A02 - Kehutanan dan Penebangan Kayu", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_K01 - Jasa Perantara Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_A01a - Tanaman Pangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_A01b - Tanaman Hortikultura", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_A01c - Tanaman Perkebunan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_A01d - Peternakan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_A03 - Perikanan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C03 - Industri Pengolahan Tembakau", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_I01 - Penyediaan Akomodasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H01 - Angkutan Rel", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H01 - Angkutan Rel", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_H01 - Angkutan Rel", "to": "_C07 - Industri Kertas dan Barang dari Kertas; Percetakan dan Reproduksi Media Rekaman", "width": 2.0}, {"color": "#E8E8E8", "from": "_H01 - Angkutan Rel", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_H01 - Angkutan Rel", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H01 - Angkutan Rel", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C04 - Industri Tekstil dan Pakaian Jadi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_E - Pengadaan Air, Pengelolaan Sampah, Limbah dan Daur Ulang", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_H02 - Angkutan Darat", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_C04 - Industri Tekstil dan Pakaian Jadi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_H03 - Angkutan Laut", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_J - Informasi dan Komunikasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_H05 - Angkutan Udara", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_I01 - Penyediaan Akomodasi", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_I01 - Penyediaan Akomodasi", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_I01 - Penyediaan Akomodasi", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_I01 - Penyediaan Akomodasi", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_I01 - Penyediaan Akomodasi", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_I01 - Penyediaan Akomodasi", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_I01 - Penyediaan Akomodasi", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_I02 - Penyediaan Makan Minum", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_I02 - Penyediaan Makan Minum", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_I02 - Penyediaan Makan Minum", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_I02 - Penyediaan Makan Minum", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_I02 - Penyediaan Makan Minum", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_I02 - Penyediaan Makan Minum", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_I02 - Penyediaan Makan Minum", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_C03 - Industri Pengolahan Tembakau", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_C09 - Industri Karet, Barang dari Karet dan Plastik", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_J - Informasi dan Komunikasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_K01 - Jasa Perantara Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_K03 - Jasa Keuangan Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_K04 - Jasa Penunjang Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_L - Real Estate", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_J - Informasi dan Komunikasi", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_A01a - Tanaman Pangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_A01b - Tanaman Hortikultura", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_A01c - Tanaman Perkebunan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_A01d - Peternakan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_A02 - Kehutanan dan Penebangan Kayu", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_A03 - Perikanan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C01 - Industri Batubara dan Pengilangan Migas", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C03 - Industri Pengolahan Tembakau", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C04 - Industri Tekstil dan Pakaian Jadi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C05 - Industri Kulit, Barang dari Kulit dan Alas Kaki", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C10 - Industri Barang Galian bukan Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C11 - Industri Logam Dasar", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C12 - Industri Barang Logam; Komputer, Barang Elektronik, Optik; dan Peralatan Listrik", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C13 - Industri Mesin dan Perlengkapan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C15 - Industri Furnitur", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_C16 - Industri Pengolahan Lainnya; Jasa Reparasi dan Pemasangan Mesin dan Peralatan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_I01 - Penyediaan Akomodasi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_K01 - Jasa Perantara Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_K03 - Jasa Keuangan Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_K04 - Jasa Penunjang Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_L - Real Estate", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_K01 - Jasa Perantara Keuangan", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_L - Real Estate", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_K02 - Asuransi dan Dana Pensiun", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_A01a - Tanaman Pangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_A01b - Tanaman Hortikultura", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_A01c - Tanaman Perkebunan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_A01d - Peternakan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_A03 - Perikanan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_C02 - Industri Makanan dan Minuman", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_C14 - Industri Alat Angkutan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_I02 - Penyediaan Makan Minum", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_K03 - Jasa Keuangan Lainnya", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_C01 - Industri Batubara dan Pengilangan Migas", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_K03 - Jasa Keuangan Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_K04 - Jasa Penunjang Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_K04 - Jasa Penunjang Keuangan", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_G01 - Perdagangan Mobil, Sepeda Motor dan Reparasinya", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_G02 - Perdagangan Besar dan Eceran, Bukan Mobil dan Sepeda Motor", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_H02 - Angkutan Darat", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_K03 - Jasa Keuangan Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_K04 - Jasa Penunjang Keuangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_L - Real Estate", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_B01 - Pertambangan Minyak, Gas dan Panas Bumi", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_B02 - Pertambangan Batubara dan Lignit", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_B03 - Pertambangan Bijih Logam", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_B04 - Pertambangan dan Penggalian Lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_C03 - Industri Pengolahan Tembakau", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_C06 - Industri Kayu, Barang dari Kayu dan Gabus dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_C08 - Industri Kimia, Farmasi dan Obat Tradisional", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_D01 - Ketenagalistrikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_K02 - Asuransi dan Dana Pensiun", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_M - Jasa Perusahaan", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_M - Jasa Perusahaan", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "to": "_H01 - Angkutan Rel", "width": 2.0}, {"color": "#E8E8E8", "from": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "to": "_L - Real Estate", "width": 2.0}, {"color": "#E8E8E8", "from": "_P - Jasa Pendidikan", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "to": "_Q - Jasa Kesehatan dan Kegiatan Sosial", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_A01e - Jasa Pertanian dan Perburuan", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_D02 - Pengadaan Gas dan Produksi Es", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_F - Konstruksi", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_H03 - Angkutan Laut", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_H04 - Angkutan Sungai Danau dan Penyeberangan", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_H05 - Angkutan Udara", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_H06 - Pergudangan dan Jasa Penunjang Angkutan; Pos dan Kurir", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_O - Administrasi Pemerintahan, Pertahanan dan Jaminan Sosial Wajib", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_P - Jasa Pendidikan", "width": 2.0}, {"color": "#E8E8E8", "from": "_R - Jasa lainnya", "to": "_R - Jasa lainnya", "width": 2.0}, {"color": "#E8E8E8", "from": "Resources", "to": "Resources", "width": 0.25}, {"color": "#E8E8E8", "from": "Manufactures", "to": "Manufactures", "width": 0.25}, {"color": "#E8E8E8", "from": "Infrastructures", "to": "Infrastructures", "width": 0.25}, {"color": "#E8E8E8", "from": "Products", "to": "Products", "width": 0.25}]);

                  nodeColors = {};
                  allNodes = nodes.get({ returnType: "Object" });
                  for (nodeId in allNodes) {
                    nodeColors[nodeId] = allNodes[nodeId].color;
                  }
                  allEdges = edges.get({ returnType: "Object" });
                  // adding nodes and edges to the graph
                  data = {nodes: nodes, edges: edges};

                  var options = {"nodes": {"borderWidth": 1, "borderWidthSelected": 2, "opacity": 100, "font": {"strokeWidth": 5, "size": 12}, "size": 25}, "edges": {"arrows": {"to": {"enabled": true, "scaleFactor": 0.5}}, "color": {"inherit": true}, "font": {"strokeWidth": 5}, "selfReferenceSize": 20, "selfReference": {"angle": 0.7853981633974483}, "smooth": {"forceDirection": "none"}}, "physics": {"barnesHut": {"springLength": 100}, "minVelocity": 0.75}};

                  


                  

                  network = new vis.Network(container, data, options);

                  

                  
                    network.on("selectNode", neighbourhoodHighlight);
                  

                  


                  

                  return network;

              }
              drawGraph();
        </script>
    </body>
</html>