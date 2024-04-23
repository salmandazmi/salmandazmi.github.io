---
layout: null
title: Dashboard
permalink: /dashboard/oce-bsi-manufacture-ecosystem
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

             
             #loadingBar {
                 position:absolute;
                 top:0px;
                 left:0px;
                 width: 100%;
                 height: 750px;
                 background-color:rgba(200,200,200,0.8);
                 -webkit-transition: all 0.5s ease;
                 -moz-transition: all 0.5s ease;
                 -ms-transition: all 0.5s ease;
                 -o-transition: all 0.5s ease;
                 transition: all 0.5s ease;
                 opacity:1;
             }

             #bar {
                 position:absolute;
                 top:0px;
                 left:0px;
                 width:20px;
                 height:20px;
                 margin:auto auto auto auto;
                 border-radius:11px;
                 border:2px solid rgba(30,30,30,0.05);
                 background: rgb(0, 173, 246); /* Old browsers */
                 box-shadow: 2px 0px 4px rgba(0,0,0,0.4);
             }

             #border {
                 position:absolute;
                 top:10px;
                 left:10px;
                 width:500px;
                 height:23px;
                 margin:auto auto auto auto;
                 box-shadow: 0px 0px 4px rgba(0,0,0,0.2);
                 border-radius:10px;
             }

             #text {
                 position:absolute;
                 top:8px;
                 left:530px;
                 width:30px;
                 height:50px;
                 margin:auto auto auto auto;
                 font-size:22px;
                 color: #000000;
             }

             div.outerBorder {
                 position:relative;
                 top:400px;
                 width:600px;
                 height:44px;
                 margin:auto auto auto auto;
                 border:8px solid rgba(0,0,0,0.1);
                 background: rgb(252,252,252); /* Old browsers */
                 background: -moz-linear-gradient(top,  rgba(252,252,252,1) 0%, rgba(237,237,237,1) 100%); /* FF3.6+ */
                 background: -webkit-gradient(linear, left top, left bottom, color-stop(0%,rgba(252,252,252,1)), color-stop(100%,rgba(237,237,237,1))); /* Chrome,Safari4+ */
                 background: -webkit-linear-gradient(top,  rgba(252,252,252,1) 0%,rgba(237,237,237,1) 100%); /* Chrome10+,Safari5.1+ */
                 background: -o-linear-gradient(top,  rgba(252,252,252,1) 0%,rgba(237,237,237,1) 100%); /* Opera 11.10+ */
                 background: -ms-linear-gradient(top,  rgba(252,252,252,1) 0%,rgba(237,237,237,1) 100%); /* IE10+ */
                 background: linear-gradient(to bottom,  rgba(252,252,252,1) 0%,rgba(237,237,237,1) 100%); /* W3C */
                 filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#fcfcfc', endColorstr='#ededed',GradientType=0 ); /* IE6-9 */
                 border-radius:72px;
                 box-shadow: 0px 0px 10px rgba(0,0,0,0.2);
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
                                
                                    <option value="C - Sektor Manufaktur">C - Sektor Manufaktur</option>
                                
                                    <option value="C10 - Industri Makanan">C10 - Industri Makanan</option>
                                
                                    <option value="C101 - Industri Pengolahan Daging">C101 - Industri Pengolahan Daging</option>
                                
                                    <option value="C1011 - Rumah Potong Daging Bukan Unggas">C1011 - Rumah Potong Daging Bukan Unggas</option>
                                
                                    <option value="C1012 - Rumah Potong Daging Unggas">C1012 - Rumah Potong Daging Unggas</option>
                                
                                    <option value="C1013 - Industri Pengolahan Produk Daging">C1013 - Industri Pengolahan Produk Daging</option>
                                
                                    <option value="C102 - Industri Pengolahan Ikan & Biota Air">C102 - Industri Pengolahan Ikan & Biota Air</option>
                                
                                    <option value="C1021 - Industri Pengolahan Ikan">C1021 - Industri Pengolahan Ikan</option>
                                
                                    <option value="C1022 - Industri Pengolahan Ikan & Biota Air dalam Kaleng ">C1022 - Industri Pengolahan Ikan & Biota Air dalam Kaleng </option>
                                
                                    <option value="C1029 - Industri Pengolahan & Pengawetan Biota Air Lainnya ">C1029 - Industri Pengolahan & Pengawetan Biota Air Lainnya </option>
                                
                                    <option value="C103 - Industri Pengolahan Buah & Sayuran ">C103 - Industri Pengolahan Buah & Sayuran </option>
                                
                                    <option value="C1031 - Industri Pengolahan Buah & Sayuran Diasinkan, Dilumatkan, Dikeringkan & Dibekukan">C1031 - Industri Pengolahan Buah & Sayuran Diasinkan, Dilumatkan, Dikeringkan & Dibekukan</option>
                                
                                    <option value="C1032 - Industri Pengolahan Buah & Sayuran dalam Kaleng ">C1032 - Industri Pengolahan Buah & Sayuran dalam Kaleng </option>
                                
                                    <option value="C1033 - Industri Pengolahan Sari Buah & Sayuran">C1033 - Industri Pengolahan Sari Buah & Sayuran</option>
                                
                                    <option value="C1039 - Industri Pengolahan Buah & Sayuran Lainnya">C1039 - Industri Pengolahan Buah & Sayuran Lainnya</option>
                                
                                    <option value="C104 - Industri Minyak Nabati & Hewani">C104 - Industri Minyak Nabati & Hewani</option>
                                
                                    <option value="C1041 - Industri Minyak Nabati & Hewani (Bukan Kelapa & Kelapa Sawit)">C1041 - Industri Minyak Nabati & Hewani (Bukan Kelapa & Kelapa Sawit)</option>
                                
                                    <option value="C1042 - Industri Kopra, Minyak Mentah, Minyak Goreng Kelapa & Pelet Kelapa">C1042 - Industri Kopra, Minyak Mentah, Minyak Goreng Kelapa & Pelet Kelapa</option>
                                
                                    <option value="C1043 - Industri Minyak Mentah & Minyak Goreng Kelapa Sawit">C1043 - Industri Minyak Mentah & Minyak Goreng Kelapa Sawit</option>
                                
                                    <option value="C1049 - Industri Minyak Mentah & Lemak Nabati & Hewani Lainnya ">C1049 - Industri Minyak Mentah & Lemak Nabati & Hewani Lainnya </option>
                                
                                    <option value="C105 - Industri Pengolahan Susu & Es Krim">C105 - Industri Pengolahan Susu & Es Krim</option>
                                
                                    <option value="C1051 - Industri Pengolahan Susu Bubuk & Susu Kental ">C1051 - Industri Pengolahan Susu Bubuk & Susu Kental </option>
                                
                                    <option value="C1052 - Industri Pengolahan Susu Bubuk & Susu Kental ">C1052 - Industri Pengolahan Susu Bubuk & Susu Kental </option>
                                
                                    <option value="C1053 - Industri Pengolahan Es Krim & Sejenisnya">C1053 - Industri Pengolahan Es Krim & Sejenisnya</option>
                                
                                    <option value="C1059 - Industri Pengolahan Produk dari Susu Lainnya ">C1059 - Industri Pengolahan Produk dari Susu Lainnya </option>
                                
                                    <option value="C106 - Industri Penggilingan Padi-Padian, Tepung & Pati">C106 - Industri Penggilingan Padi-Padian, Tepung & Pati</option>
                                
                                    <option value="C1061 - Industri Penggilingan Serelia & Biji-Bijian Lainnya">C1061 - Industri Penggilingan Serelia & Biji-Bijian Lainnya</option>
                                
                                    <option value="C1062 - Industri Pati">C1062 - Industri Pati</option>
                                
                                    <option value="C1063 - Industri Penggilingan dan Industri Tepung Beras & Jagung ">C1063 - Industri Penggilingan dan Industri Tepung Beras & Jagung </option>
                                
                                    <option value="C107 - Industri Makanan Lainnya">C107 - Industri Makanan Lainnya</option>
                                
                                    <option value="C1071 - Industri Produk Roti & Kue">C1071 - Industri Produk Roti & Kue</option>
                                
                                    <option value="C1072 - Industri Gula ">C1072 - Industri Gula </option>
                                
                                    <option value="C1073 - Industri Kakao, Cokelat & Kembang Gula ">C1073 - Industri Kakao, Cokelat & Kembang Gula </option>
                                
                                    <option value="C1074 - Industri Makaroni, Mie & Sejenisnya ">C1074 - Industri Makaroni, Mie & Sejenisnya </option>
                                
                                    <option value="C1075 - Industri Makanan & Masakan Olahan">C1075 - Industri Makanan & Masakan Olahan</option>
                                
                                    <option value="C1076 - Industri Pengolahan Kopi, Teh & Herbal">C1076 - Industri Pengolahan Kopi, Teh & Herbal</option>
                                
                                    <option value="C1077 - Industri Bumbu & Produk Masak Lainnya ">C1077 - Industri Bumbu & Produk Masak Lainnya </option>
                                
                                    <option value="C1079 - Industri Produk Makanan Lainnya">C1079 - Industri Produk Makanan Lainnya</option>
                                
                                    <option value="C108 - Industri Makanan Hewan">C108 - Industri Makanan Hewan</option>
                                
                                    <option value="C11 - Industri Minuman">C11 - Industri Minuman</option>
                                
                                    <option value="C1101 - Industri Minuman Beralkohol Hasil Destilasi ">C1101 - Industri Minuman Beralkohol Hasil Destilasi </option>
                                
                                    <option value="C1102 - Industri Minuman Beralkohol Hasil Fermentasi Anggur">C1102 - Industri Minuman Beralkohol Hasil Fermentasi Anggur</option>
                                
                                    <option value="C1103 - Industri Minuman Beralkohol Hasil Fermentasi Malt & Industri Malt ">C1103 - Industri Minuman Beralkohol Hasil Fermentasi Malt & Industri Malt </option>
                                
                                    <option value="C1104 - Industri Minuman Ringan ">C1104 - Industri Minuman Ringan </option>
                                
                                    <option value="C1105 - Industri Air Kemasan & Air Minum Isi Ulang ">C1105 - Industri Air Kemasan & Air Minum Isi Ulang </option>
                                
                                    <option value="C1109 - Industri Minuman Lainnya ">C1109 - Industri Minuman Lainnya </option>
                                
                                    <option value="C12 - Industri Pengolahan Tembakau">C12 - Industri Pengolahan Tembakau</option>
                                
                                    <option value="C1201 - Industri Rokok & Produk Tembakau Lainnya ">C1201 - Industri Rokok & Produk Tembakau Lainnya </option>
                                
                                    <option value="C1209 - Industri Pengolahan Tembakau Lainnya ">C1209 - Industri Pengolahan Tembakau Lainnya </option>
                                
                                    <option value="C13 - Industri Tekstil">C13 - Industri Tekstil</option>
                                
                                    <option value="C131 - Industri Pemintalan, Pertenunan & Penyempurnaan Tekstil ">C131 - Industri Pemintalan, Pertenunan & Penyempurnaan Tekstil </option>
                                
                                    <option value="C1311 - Industri Persiapan & Pemintalan Serat Tekstil ">C1311 - Industri Persiapan & Pemintalan Serat Tekstil </option>
                                
                                    <option value="C1312 - Industri Pertenunan Tekstil ">C1312 - Industri Pertenunan Tekstil </option>
                                
                                    <option value="C1313 - Industri Penyempurnaan Tekstil">C1313 - Industri Penyempurnaan Tekstil</option>
                                
                                    <option value="C139 - Industri Tekstil Lainnya ">C139 - Industri Tekstil Lainnya </option>
                                
                                    <option value="C1391 - Industri Kain Rajutan & Sulaman ">C1391 - Industri Kain Rajutan & Sulaman </option>
                                
                                    <option value="C1392 - Industri Pembuatan Barang Tekstil, Bukan Pakaian Jadi ">C1392 - Industri Pembuatan Barang Tekstil, Bukan Pakaian Jadi </option>
                                
                                    <option value="C1393 - Industri Karpet & Permadani ">C1393 - Industri Karpet & Permadani </option>
                                
                                    <option value="C1394 - Industri Tali & Barang dari Tali">C1394 - Industri Tali & Barang dari Tali</option>
                                
                                    <option value="C1399 - Industri Tekstil Lainnya Ytdl">C1399 - Industri Tekstil Lainnya Ytdl</option>
                                
                                    <option value="C14 - Industri Pakaian Jadi ">C14 - Industri Pakaian Jadi </option>
                                
                                    <option value="C141 - Industri Pakaian Jadi & Perlengkapannya">C141 - Industri Pakaian Jadi & Perlengkapannya</option>
                                
                                    <option value="C1411 - Industri Pakaian Jadi (Bukan Penjahitan & Pembuatan Pakaian) ">C1411 - Industri Pakaian Jadi (Bukan Penjahitan & Pembuatan Pakaian) </option>
                                
                                    <option value="C1412 - Penjahitan & Pembuatan Pakaian Sesuai Pesanan ">C1412 - Penjahitan & Pembuatan Pakaian Sesuai Pesanan </option>
                                
                                    <option value="C1413 - Industri Perlengkapan Pakaian">C1413 - Industri Perlengkapan Pakaian</option>
                                
                                    <option value="C142 - Industri Pakaian Jadi & Barang dari Kulit Berbulu">C142 - Industri Pakaian Jadi & Barang dari Kulit Berbulu</option>
                                
                                    <option value="C143 - Industri Pakaian Jadi Rajutan & Sulaman/Bordir">C143 - Industri Pakaian Jadi Rajutan & Sulaman/Bordir</option>
                                
                                    <option value="C15 - Industri Kulit, Barang dari Kulit & Alas Kaki">C15 - Industri Kulit, Barang dari Kulit & Alas Kaki</option>
                                
                                    <option value="C151 - Industri Kulit & Barang dari Kulit">C151 - Industri Kulit & Barang dari Kulit</option>
                                
                                    <option value="C1511 - Industri Kulit & Kulit Komposisi">C1511 - Industri Kulit & Kulit Komposisi</option>
                                
                                    <option value="C1512 - Industri Barang dari Kulit, Koper, Tas Tangan & Sejenisnya">C1512 - Industri Barang dari Kulit, Koper, Tas Tangan & Sejenisnya</option>
                                
                                    <option value="C152 - Industri Alas Kaki">C152 - Industri Alas Kaki</option>
                                
                                    <option value="C16 - Industri Kayu, Gabus, Bambu, Rotan & Sejenisnya ">C16 - Industri Kayu, Gabus, Bambu, Rotan & Sejenisnya </option>
                                
                                    <option value="C161 - Industri Penggergajian Kayu, Rotan, Bambu & Sejenisnya ">C161 - Industri Penggergajian Kayu, Rotan, Bambu & Sejenisnya </option>
                                
                                    <option value="C162 - Industri Barang dari Kayu & Sejenis Lainnya">C162 - Industri Barang dari Kayu & Sejenis Lainnya</option>
                                
                                    <option value="C1621 - Industri Veneer, Kayu Lapis, Kayu Laminasi & Sejenisnya ">C1621 - Industri Veneer, Kayu Lapis, Kayu Laminasi & Sejenisnya </option>
                                
                                    <option value="C1622 - Industri Barang Bangunan dari Kayu">C1622 - Industri Barang Bangunan dari Kayu</option>
                                
                                    <option value="C1623 - Industri Wadah dari Kayu ">C1623 - Industri Wadah dari Kayu </option>
                                
                                    <option value="C1629 - ndustri Barang Lainnya dari Kayu & Sejenisnya ">C1629 - ndustri Barang Lainnya dari Kayu & Sejenisnya </option>
                                
                                    <option value="C17 - Industri Kertas & Barang dari Kertas">C17 - Industri Kertas & Barang dari Kertas</option>
                                
                                    <option value="C1701 - Industri Bubur Kertas, Kertas & Papan Kertas ">C1701 - Industri Bubur Kertas, Kertas & Papan Kertas </option>
                                
                                    <option value="C1702 - Industri Kertas, Papan Kertas & Wadah dari Kertas">C1702 - Industri Kertas, Papan Kertas & Wadah dari Kertas</option>
                                
                                    <option value="C1709 - Industri Barang dari Kertas & Papan Kertas Lainnya ">C1709 - Industri Barang dari Kertas & Papan Kertas Lainnya </option>
                                
                                    <option value="C18 -  Industri Pencetakan & Reproduksi Media Rekaman">C18 -  Industri Pencetakan & Reproduksi Media Rekaman</option>
                                
                                    <option value="C181 - Industri Pencetakan & Kegiatan Ybdi">C181 - Industri Pencetakan & Kegiatan Ybdi</option>
                                
                                    <option value="C1811 - Industri Pencetakan ">C1811 - Industri Pencetakan </option>
                                
                                    <option value="C1812 - Kegiatan Jasa Penunjang Pencetakan ">C1812 - Kegiatan Jasa Penunjang Pencetakan </option>
                                
                                    <option value="C182 - Reproduksi Media Rekaman">C182 - Reproduksi Media Rekaman</option>
                                
                                    <option value="C19 - Industri Produk dari Batu Bara & Pengilangan Minyak Bumi ">C19 - Industri Produk dari Batu Bara & Pengilangan Minyak Bumi </option>
                                
                                    <option value="C191 - Industri Produk dari Batu Bara">C191 - Industri Produk dari Batu Bara</option>
                                
                                    <option value="C192 - Industri Produk Pengilangan Minyak Bumi">C192 - Industri Produk Pengilangan Minyak Bumi</option>
                                
                                    <option value="C1921 - Industri Bahan Bakar & Minyak Pelumas">C1921 - Industri Bahan Bakar & Minyak Pelumas</option>
                                
                                    <option value="C1929 - Industri Produk Pengilangan Minyak Bumi Lainnya ">C1929 - Industri Produk Pengilangan Minyak Bumi Lainnya </option>
                                
                                    <option value="C20 - Industri Bahan Kimia & Barang dari Bahan Kimia">C20 - Industri Bahan Kimia & Barang dari Bahan Kimia</option>
                                
                                    <option value="C201 - Industri Bahan Kimia">C201 - Industri Bahan Kimia</option>
                                
                                    <option value="C2011 - Industri Kimia Dasar">C2011 - Industri Kimia Dasar</option>
                                
                                    <option value="C2012 - Industri Pupuk & Bahan Senyawa Nitrogen ">C2012 - Industri Pupuk & Bahan Senyawa Nitrogen </option>
                                
                                    <option value="C2013 - Industri Plastik & Karet Buatan dalam Bentuk Dasar ">C2013 - Industri Plastik & Karet Buatan dalam Bentuk Dasar </option>
                                
                                    <option value="C202 - Industri Barang Kimia Lainnya">C202 - Industri Barang Kimia Lainnya</option>
                                
                                    <option value="C2021 - Industri Pestisida & Produk Agrokimia Lainnya ">C2021 - Industri Pestisida & Produk Agrokimia Lainnya </option>
                                
                                    <option value="C2022 - Industri Cat & Tinta Cetak, Pernis & Bahan Pelapisan Sejenisnya & Lak ">C2022 - Industri Cat & Tinta Cetak, Pernis & Bahan Pelapisan Sejenisnya & Lak </option>
                                
                                    <option value="C2023 - Industri Sabun & Deterjen, Bahan Pembersih & Pengilap, Parfum & Kosmetik ">C2023 - Industri Sabun & Deterjen, Bahan Pembersih & Pengilap, Parfum & Kosmetik </option>
                                
                                    <option value="C2029 - Industri Barang Kimia Lainnya Ytdl ">C2029 - Industri Barang Kimia Lainnya Ytdl </option>
                                
                                    <option value="C203 - Industri Serat Buatan">C203 - Industri Serat Buatan</option>
                                
                                    <option value="C21 - Industri Farmasi, Produk Obat Kimia & Obat Tradisional">C21 - Industri Farmasi, Produk Obat Kimia & Obat Tradisional</option>
                                
                                    <option value="C2101 - Industri Farmasi & Produk Obat Kimia ">C2101 - Industri Farmasi & Produk Obat Kimia </option>
                                
                                    <option value="C2102 - Industri Obat Tradisional ">C2102 - Industri Obat Tradisional </option>
                                
                                    <option value="C22 - Industri Karet, Barang dari Karet & Plastik">C22 - Industri Karet, Barang dari Karet & Plastik</option>
                                
                                    <option value="C221 - Industri Karet & Barang dari Karet">C221 - Industri Karet & Barang dari Karet</option>
                                
                                    <option value="C2211 - Industri Ban & Vulkanisir Ban ">C2211 - Industri Ban & Vulkanisir Ban </option>
                                
                                    <option value="C2212 - Industri Pengasapan, Remilling & Karet Remah ">C2212 - Industri Pengasapan, Remilling & Karet Remah </option>
                                
                                    <option value="C2219 - Industri Barang dari Karet Lainnya ">C2219 - Industri Barang dari Karet Lainnya </option>
                                
                                    <option value="C222 - Industri Barang dari Plastik">C222 - Industri Barang dari Plastik</option>
                                
                                    <option value="C2221 - Industri Barang dari Plastik untuk Bangunan">C2221 - Industri Barang dari Plastik untuk Bangunan</option>
                                
                                    <option value="C2222 - Industri Barang dari Plastik untuk Pengemasan ">C2222 - Industri Barang dari Plastik untuk Pengemasan </option>
                                
                                    <option value="C2223 - Industri Pipa Plastik & Perlengkapannya ">C2223 - Industri Pipa Plastik & Perlengkapannya </option>
                                
                                    <option value="C2229 - Industri Barang dari Plastik Lainnya ">C2229 - Industri Barang dari Plastik Lainnya </option>
                                
                                    <option value="C23 - Industri Barang Galian Bukan Logam">C23 - Industri Barang Galian Bukan Logam</option>
                                
                                    <option value="C231 - Industri Kaca & Barang dari Kaca">C231 - Industri Kaca & Barang dari Kaca</option>
                                
                                    <option value="C2311 - Industri Kaca">C2311 - Industri Kaca</option>
                                
                                    <option value="C2312 - Industri Barang dari Kaca">C2312 - Industri Barang dari Kaca</option>
                                
                                    <option value="C239 - Industri Barang Galian Bukan Logam Lainnya ">C239 - Industri Barang Galian Bukan Logam Lainnya </option>
                                
                                    <option value="C2391 - Industri Barang Refraktori">C2391 - Industri Barang Refraktori</option>
                                
                                    <option value="C2392 - Industri Bahan Bangunan dari Tanah Liat/Keramik ">C2392 - Industri Bahan Bangunan dari Tanah Liat/Keramik </option>
                                
                                    <option value="C2393 - Industri Barang Tanah Liat/Keramik & Porselen">C2393 - Industri Barang Tanah Liat/Keramik & Porselen</option>
                                
                                    <option value="C2394 - Industri Semen, Kapur & Gips ">C2394 - Industri Semen, Kapur & Gips </option>
                                
                                    <option value="C2395 - Industri Barang dari Semen, Kapur, Gips & Asbes ">C2395 - Industri Barang dari Semen, Kapur, Gips & Asbes </option>
                                
                                    <option value="C2396 - Industri Barang dari Batu ">C2396 - Industri Barang dari Batu </option>
                                
                                    <option value="C2399 - Industri Barang Galian Bukan Logam Lainnya Ytdl ">C2399 - Industri Barang Galian Bukan Logam Lainnya Ytdl </option>
                                
                                    <option value="C24 - Industri Logam Dasar">C24 - Industri Logam Dasar</option>
                                
                                    <option value="C241 - Industri Logam Dasar Besi & Baja">C241 - Industri Logam Dasar Besi & Baja</option>
                                
                                    <option value="C2410 - Industri Logam Dasar Besi & Baja">C2410 - Industri Logam Dasar Besi & Baja</option>
                                
                                    <option value="C242 - Industri Logam Dasar Mulia & Logam Dasar Bukan Besi Lainnya ">C242 - Industri Logam Dasar Mulia & Logam Dasar Bukan Besi Lainnya </option>
                                
                                    <option value="C2420 - Industri Logam Dasar Mulia & Logam Dasar Bukan Besi Lainnya ">C2420 - Industri Logam Dasar Mulia & Logam Dasar Bukan Besi Lainnya </option>
                                
                                    <option value="C243 - Industri Pengecoran Logam">C243 - Industri Pengecoran Logam</option>
                                
                                    <option value="C2431 - Industri Pengecoran Besi & Baja">C2431 - Industri Pengecoran Besi & Baja</option>
                                
                                    <option value="C2432 - Industri Pengecoran Logam Bukan Besi & Baja ">C2432 - Industri Pengecoran Logam Bukan Besi & Baja </option>
                                
                                    <option value="C25 - Industri Barang Logam, Bukan Mesin & Peralatannya">C25 - Industri Barang Logam, Bukan Mesin & Peralatannya</option>
                                
                                    <option value="C251 - Industri Barang Logam Siap Pasang">C251 - Industri Barang Logam Siap Pasang</option>
                                
                                    <option value="C2511 - Industri Barang Logam Siap Pasang untuk Bangunan ">C2511 - Industri Barang Logam Siap Pasang untuk Bangunan </option>
                                
                                    <option value="C2512 - Industri Tangki, Tandon Air & Wadah dari Logam ">C2512 - Industri Tangki, Tandon Air & Wadah dari Logam </option>
                                
                                    <option value="C2513 - Industri Generator Uap, Bukan Ketel Pemanas ">C2513 - Industri Generator Uap, Bukan Ketel Pemanas </option>
                                
                                    <option value="C252 - Industri Senjata & Amunisi">C252 - Industri Senjata & Amunisi</option>
                                
                                    <option value="C259 - Industri Barang Logam Lainnya & Jasa Pembuatan Barang Logam ">C259 - Industri Barang Logam Lainnya & Jasa Pembuatan Barang Logam </option>
                                
                                    <option value="C2591 - Industri Penempaan, Pengepresan, Pencetakan & Pembentukan Logam">C2591 - Industri Penempaan, Pengepresan, Pencetakan & Pembentukan Logam</option>
                                
                                    <option value="C2592 - Jasa Industri untuk Berbagai Pengerjaan Khusus Barang Logam">C2592 - Jasa Industri untuk Berbagai Pengerjaan Khusus Barang Logam</option>
                                
                                    <option value="C2593 - Industri Alat Potong, Perkakas Tangan & Peralatan Umum ">C2593 - Industri Alat Potong, Perkakas Tangan & Peralatan Umum </option>
                                
                                    <option value="C2594 - Industri Ember, Kaleng, Drum & Wadah Sejenis dari Logam ">C2594 - Industri Ember, Kaleng, Drum & Wadah Sejenis dari Logam </option>
                                
                                    <option value="C2595 - Industri Barang dari Kawat & Paku, Mur & Baut">C2595 - Industri Barang dari Kawat & Paku, Mur & Baut</option>
                                
                                    <option value="C2599 - Industri Barang Logam Lainnya">C2599 - Industri Barang Logam Lainnya</option>
                                
                                    <option value="C26 - Industri Komputer, Barang Elektronik & Optik">C26 - Industri Komputer, Barang Elektronik & Optik</option>
                                
                                    <option value="C261 - Industri Komponen & Papan Elektronik">C261 - Industri Komponen & Papan Elektronik</option>
                                
                                    <option value="C2611 - Industri Tabung Elektron & Konektor Elektronik ">C2611 - Industri Tabung Elektron & Konektor Elektronik </option>
                                
                                    <option value="C2612 - Industri Semi Konduktor & Komponen Elektronik Lainnya ">C2612 - Industri Semi Konduktor & Komponen Elektronik Lainnya </option>
                                
                                    <option value="C262 - Industri Komputer & Perlengkapannya">C262 - Industri Komputer & Perlengkapannya</option>
                                
                                    <option value="C2621 - Industri Komputer & Perakitan Komputer">C2621 - Industri Komputer & Perakitan Komputer</option>
                                
                                    <option value="C2622 - Industri Perlengkapan Komputer">C2622 - Industri Perlengkapan Komputer</option>
                                
                                    <option value="C263 - Industri Peralatan Komunikasi">C263 - Industri Peralatan Komunikasi</option>
                                
                                    <option value="C2631 - Industri Peralatan Telepon & Faksimili ">C2631 - Industri Peralatan Telepon & Faksimili </option>
                                
                                    <option value="C2632 - Industri Peralatan Komunikasi Tanpa Kabel">C2632 - Industri Peralatan Komunikasi Tanpa Kabel</option>
                                
                                    <option value="C2639 - Industri Peralatan Komunikasi Lainnya">C2639 - Industri Peralatan Komunikasi Lainnya</option>
                                
                                    <option value="C264 - Industri Peralatan Audio & Video Elektronik">C264 - Industri Peralatan Audio & Video Elektronik</option>
                                
                                    <option value="C2641 - Industri Televisi & Perakitan Televisi">C2641 - Industri Televisi & Perakitan Televisi</option>
                                
                                    <option value="C2642 - Industri Peralatan Perekam Audio & Video">C2642 - Industri Peralatan Perekam Audio & Video</option>
                                
                                    <option value="C2649 - Industri Peralatan Audio & Video Elektronik Lainnya">C2649 - Industri Peralatan Audio & Video Elektronik Lainnya</option>
                                
                                    <option value="C265 - Industri Alat Ukur, Alat Uji, Alat Navigasi & Alat Ukur Waktu ">C265 - Industri Alat Ukur, Alat Uji, Alat Navigasi & Alat Ukur Waktu </option>
                                
                                    <option value="C2651 - Industri Alat Ukur, Alat Uji, Alat Navigasi & Kontrol ">C2651 - Industri Alat Ukur, Alat Uji, Alat Navigasi & Kontrol </option>
                                
                                    <option value="C2652 - Industri Alat Ukur Waktu">C2652 - Industri Alat Ukur Waktu</option>
                                
                                    <option value="C266 - Industri Peralatan Iradiasi, Elektromedikal & Elektroterapi ">C266 - Industri Peralatan Iradiasi, Elektromedikal & Elektroterapi </option>
                                
                                    <option value="C267 - Industri Peralatan Fotografi & Instrumen Optik Bukan Kaca Mata ">C267 - Industri Peralatan Fotografi & Instrumen Optik Bukan Kaca Mata </option>
                                
                                    <option value="C2671 - Industri Peralatan Fotografi">C2671 - Industri Peralatan Fotografi</option>
                                
                                    <option value="C2679 - Industri Peralatan Fotografi & Instrumen Optik Lainnya">C2679 - Industri Peralatan Fotografi & Instrumen Optik Lainnya</option>
                                
                                    <option value="C268 - Industri Media Magnetik & Media Optik">C268 - Industri Media Magnetik & Media Optik</option>
                                
                                    <option value="C27 - Industri Peralatan Listrik">C27 - Industri Peralatan Listrik</option>
                                
                                    <option value="C271 - Industri Motor Listrik, Generator, Transformator & Pendistribusian Listrik">C271 - Industri Motor Listrik, Generator, Transformator & Pendistribusian Listrik</option>
                                
                                    <option value="C2711 - Industri Motor Listrik, Generator & Transformator ">C2711 - Industri Motor Listrik, Generator & Transformator </option>
                                
                                    <option value="C2712 - Industri Peralatan Pengontrol & Pendistribusian Listrik ">C2712 - Industri Peralatan Pengontrol & Pendistribusian Listrik </option>
                                
                                    <option value="C272 - Industri Batu Baterai & Akumulator Listrik">C272 - Industri Batu Baterai & Akumulator Listrik</option>
                                
                                    <option value="C273 - Industri Kabel & Perlengkapannya">C273 - Industri Kabel & Perlengkapannya</option>
                                
                                    <option value="C2731 - Industri Kabel Serat Optik">C2731 - Industri Kabel Serat Optik</option>
                                
                                    <option value="C2732 - Industri Kabel Listrik & Elektronik Lainnya ">C2732 - Industri Kabel Listrik & Elektronik Lainnya </option>
                                
                                    <option value="C2733 - Industri Perlengkapan Kabel">C2733 - Industri Perlengkapan Kabel</option>
                                
                                    <option value="C274 - Industri Peralatan Penerangan Listrik ">C274 - Industri Peralatan Penerangan Listrik </option>
                                
                                    <option value="C275 - Industri Peralatan Rumah Tangga">C275 - Industri Peralatan Rumah Tangga</option>
                                
                                    <option value="C2751 - Industri Peralatan Listrik Rumah Tangga ">C2751 - Industri Peralatan Listrik Rumah Tangga </option>
                                
                                    <option value="C2752 - Industri Peralatan Elektrotermal Rumah Tangga ">C2752 - Industri Peralatan Elektrotermal Rumah Tangga </option>
                                
                                    <option value="C2753 - Industri Peralatan Pemanas & Masak Bukan Listrik Rumah Tangga ">C2753 - Industri Peralatan Pemanas & Masak Bukan Listrik Rumah Tangga </option>
                                
                                    <option value="C279 - Industri Peralatan Listrik Lainnya">C279 - Industri Peralatan Listrik Lainnya</option>
                                
                                    <option value="C28 - Industri Mesin & Perlengkapan Lainnya">C28 - Industri Mesin & Perlengkapan Lainnya</option>
                                
                                    <option value="C281 - Industri Mesin untuk Keperluan Umum">C281 - Industri Mesin untuk Keperluan Umum</option>
                                
                                    <option value="C2811 - Industri Mesin & Turbin, Bukan Mesin Pesawat & Kendaraan Bermotor">C2811 - Industri Mesin & Turbin, Bukan Mesin Pesawat & Kendaraan Bermotor</option>
                                
                                    <option value="C2812 - Industri Peralatan Tenaga Zat Cair & Gas ">C2812 - Industri Peralatan Tenaga Zat Cair & Gas </option>
                                
                                    <option value="C2813 - Industri Pompa Lainnya, Kompresor, Kran & Klep/Katup ">C2813 - Industri Pompa Lainnya, Kompresor, Kran & Klep/Katup </option>
                                
                                    <option value="C2814 - Industri Bearing, Roda Gigi & Elemen Penggerak Mesin ">C2814 - Industri Bearing, Roda Gigi & Elemen Penggerak Mesin </option>
                                
                                    <option value="C2815 - Industri Oven, Perapian & Tungku Pembakar">C2815 - Industri Oven, Perapian & Tungku Pembakar</option>
                                
                                    <option value="C2816 - Industri Alat Pengangkat & Pemindah">C2816 - Industri Alat Pengangkat & Pemindah</option>
                                
                                    <option value="C2817 - Industri Mesin & Peralatan Kantor (Bukan Komputer)">C2817 - Industri Mesin & Peralatan Kantor (Bukan Komputer)</option>
                                
                                    <option value="C2818 - Industri Perkakas Tangan yang Digerakkan Tenaga ">C2818 - Industri Perkakas Tangan yang Digerakkan Tenaga </option>
                                
                                    <option value="C2819 - Industri Mesin untuk Keperluan Umum Lainnya ">C2819 - Industri Mesin untuk Keperluan Umum Lainnya </option>
                                
                                    <option value="C282 - Industri Mesin untuk Keperluan Khusus">C282 - Industri Mesin untuk Keperluan Khusus</option>
                                
                                    <option value="C2821 - Industri Mesin Pertanian & Kehutanan">C2821 - Industri Mesin Pertanian & Kehutanan</option>
                                
                                    <option value="C2822 - Industri Mesin & Perkakas Mesin">C2822 - Industri Mesin & Perkakas Mesin</option>
                                
                                    <option value="C2823 - Industri Mesin Metalurgi ">C2823 - Industri Mesin Metalurgi </option>
                                
                                    <option value="C2824 - Industri Mesin Penambangan, Penggalian & Konstruksi ">C2824 - Industri Mesin Penambangan, Penggalian & Konstruksi </option>
                                
                                    <option value="C2825 - Industri Mesin Pengolahan Makanan, Minuman & Tembakau ">C2825 - Industri Mesin Pengolahan Makanan, Minuman & Tembakau </option>
                                
                                    <option value="C2826 - Industri Mesin Tekstil, Pakaian Jadi & Produk Kulit ">C2826 - Industri Mesin Tekstil, Pakaian Jadi & Produk Kulit </option>
                                
                                    <option value="C2829 - Industri Mesin Keperluan Khusus Lainnya ">C2829 - Industri Mesin Keperluan Khusus Lainnya </option>
                                
                                    <option value="C29 - Industri Kendaraan Bermotor, Trailer & Semi Trailer">C29 - Industri Kendaraan Bermotor, Trailer & Semi Trailer</option>
                                
                                    <option value="C291 - Industri Kendaraan Bermotor Roda Empat atau Lebih">C291 - Industri Kendaraan Bermotor Roda Empat atau Lebih</option>
                                
                                    <option value="C292 - Industri Karoseri Kendaraan Bermotor Roda Empat atau Lebih">C292 - Industri Karoseri Kendaraan Bermotor Roda Empat atau Lebih</option>
                                
                                    <option value="C293 - Industri Suku Cadang & Aksesori Kendaraan Bermotor Roda Empat atau Lebih ">C293 - Industri Suku Cadang & Aksesori Kendaraan Bermotor Roda Empat atau Lebih </option>
                                
                                    <option value="C30 - Industri Alat Angkutan Lainnya">C30 - Industri Alat Angkutan Lainnya</option>
                                
                                    <option value="C301 - Industri Pembuatan Kapal & Perahu">C301 - Industri Pembuatan Kapal & Perahu</option>
                                
                                    <option value="C3011 - Industri Pembuatan Kapal, Perahu & Bangunan Terapung ">C3011 - Industri Pembuatan Kapal, Perahu & Bangunan Terapung </option>
                                
                                    <option value="C3012 - Industri Pembuatan Kapal & Perahu untuk Wisata, Rekreasi & Olahraga ">C3012 - Industri Pembuatan Kapal & Perahu untuk Wisata, Rekreasi & Olahraga </option>
                                
                                    <option value="C302 - Industri Lokomotif & Gerbong Kereta">C302 - Industri Lokomotif & Gerbong Kereta</option>
                                
                                    <option value="C303 - Industri Pesawat Terbang & Perlengkapannya">C303 - Industri Pesawat Terbang & Perlengkapannya</option>
                                
                                    <option value="C304 - Industri Kendaraan Perang">C304 - Industri Kendaraan Perang</option>
                                
                                    <option value="C309 - Industri Alat Angkutan Lainnya Ytdl">C309 - Industri Alat Angkutan Lainnya Ytdl</option>
                                
                                    <option value="C3091 - Industri Sepeda Motor ">C3091 - Industri Sepeda Motor </option>
                                
                                    <option value="C3092 - Industri Sepeda & Kursi Roda ">C3092 - Industri Sepeda & Kursi Roda </option>
                                
                                    <option value="C3099 - Industri Alat Angkutan Lainnya Ytdl">C3099 - Industri Alat Angkutan Lainnya Ytdl</option>
                                
                                    <option value="C31 - Industri Furnitur">C31 - Industri Furnitur</option>
                                
                                    <option value="C32 - Industri Pengolahan Lainnya">C32 - Industri Pengolahan Lainnya</option>
                                
                                    <option value="C321 - Industri Barang Perhiasan & Barang Berharga">C321 - Industri Barang Perhiasan & Barang Berharga</option>
                                
                                    <option value="C3211 - Industri Perhiasan & Barang Sejenis ">C3211 - Industri Perhiasan & Barang Sejenis </option>
                                
                                    <option value="C3212 - Industri Perhiasan Imitasi & Barang Sejenis ">C3212 - Industri Perhiasan Imitasi & Barang Sejenis </option>
                                
                                    <option value="C322 - Industri Alat Musik">C322 - Industri Alat Musik</option>
                                
                                    <option value="C323 - Industri Alat Olahraga">C323 - Industri Alat Olahraga</option>
                                
                                    <option value="C324 - Industri Alat Permainan & Mainan Anak-Anak">C324 - Industri Alat Permainan & Mainan Anak-Anak</option>
                                
                                    <option value="C325 - Industri Peralatan Kedokteran serta Perlengkapannya">C325 - Industri Peralatan Kedokteran serta Perlengkapannya</option>
                                
                                    <option value="C329 - Industri Pengolahan Lainnya">C329 - Industri Pengolahan Lainnya</option>
                                
                                    <option value="C33 - Reparasi & Pemasangan Mesin & Peralatan">C33 - Reparasi & Pemasangan Mesin & Peralatan</option>
                                
                                    <option value="C331 - Reparasi Produk Logam Pabrikasi, Mesin & Peralatan ">C331 - Reparasi Produk Logam Pabrikasi, Mesin & Peralatan </option>
                                
                                    <option value="C3311 - Reparasi Produk Logam Pabrikasi ">C3311 - Reparasi Produk Logam Pabrikasi </option>
                                
                                    <option value="C3312 - Reparasi Mesin">C3312 - Reparasi Mesin</option>
                                
                                    <option value="C3313 - Reparasi Peralatan Elektronik & Optik">C3313 - Reparasi Peralatan Elektronik & Optik</option>
                                
                                    <option value="C3314 - Reparasi Peralatan Listrik ">C3314 - Reparasi Peralatan Listrik </option>
                                
                                    <option value="C3315 - Reparasi Alat Angkutan, Bukan Kendaraan Bermotor ">C3315 - Reparasi Alat Angkutan, Bukan Kendaraan Bermotor </option>
                                
                                    <option value="C3319 - Reparasi Peralatan Lainnya ">C3319 - Reparasi Peralatan Lainnya </option>
                                
                                    <option value="C332 - Instalasi/Pemasangan Mesin & Peralatan Industri">C332 - Instalasi/Pemasangan Mesin & Peralatan Industri</option>
                                
                            </select>
                        </div>
                        <div class="col-2 pb-2">
                            <button type="button" class="btn btn-primary btn-block" onclick="neighbourhoodHighlight({nodes: []});">Reset Selection</button>
                        </div>
                    </div>
                </div>
            
            
            <div id="mynetwork" class="card-body"></div>
        </div>

        
            <div id="loadingBar">
              <div class="outerBorder">
                <div id="text">0%</div>
                <div id="border">
                  <div id="bar"></div>
                </div>
              </div>
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
                  nodes = new vis.DataSet([{"color": "#F8AD3C", "id": "C - Sektor Manufaktur", "label": "Sektor Manufaktur\nRp7755.49 Tn", "shape": "dot", "size": 40.0, "title": "Industri Pengolahan"}, {"color": "#F8AD3C", "id": "C10 - Industri Makanan", "label": "Industri Makanan\nRp2051.84 Tn", "physics": false, "shape": "dot", "size": 40.0, "title": "Industri Makanan", "x": 650.0, "y": -7.534077267803125e-07}, {"color": "#F8AD3C", "id": "C101 - Industri Pengolahan Daging", "label": "Industri Pengolahan Daging\nRp260.59 Tn", "shape": "dot", "size": 13.810082540782831, "title": "Industri Pengolahan dan Pengawetan Daging"}, {"color": "#F8AD3C", "id": "C1011 - Rumah Potong Daging Bukan Unggas", "label": "Rumah Potong Daging\nBukan Unggas\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Kegiatan Rumah Potong dan Pengepakan Daging Bukan Unggas"}, {"color": "#F8AD3C", "id": "C1012 - Rumah Potong Daging Unggas", "label": "Rumah Potong Daging\nUnggas\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Kegiatan Rumah Potong dan Pengepakan Daging Unggas"}, {"color": "#F8AD3C", "id": "C1013 - Industri Pengolahan Produk Daging", "label": "Industri Pengolahan\nProduk Daging\nRp80.38 Tn", "shape": "dot", "size": 11.175216831956595, "title": "Industri Pengolahan dan Pengawetan Produk Daging dan Daging Unggas"}, {"color": "#F8AD3C", "id": "C102 - Industri Pengolahan Ikan \u0026 Biota Air", "label": "Industri Pengolahan\nIkan \u0026 Biota Air\nRp97.98 Tn", "shape": "dot", "size": 11.432573279344671, "title": "Industri Pengolahan dan Pengawetan Ikan dan Biota Air"}, {"color": "#F8AD3C", "id": "C1021 - Industri Pengolahan Ikan", "label": "Industri Pengolahan Ikan\nRp22.03 Tn", "shape": "dot", "size": 10.32210006778018, "title": "Industri Pengolahan dan Pengawetan Ikan dan Produk Ikan\n\nSub-industri:\nC10211 - Industri Penggaraman/Pengeringan Ikan\nC10212 - Industri Pengasapan/Pemanggangan Ikan\nC10213 - Industri Pembekuan Ikan\nC10214 - Industri Pemindangan Ikan\nC10215 - Industri Peragian/Fermentasi Ikan\nC10216 - Industri Berbasis Daging Lumatan \u0026 Surimi\nC10217 - Industri Pendinginan/Pengesan Ikan\nC10219 - Industri Pengolahan Ikan Lainnya"}, {"color": "#F8AD3C", "id": "C1022 - Industri Pengolahan Ikan \u0026 Biota Air dalam Kaleng ", "label": "Industri Pengolahan Ikan\n\u0026 Biota Air dalam Kaleng \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan dan Pengawetan Ikan dan Biota Air Dalam Kaleng \n\nSub-Industri:\nC10221 - Industri Pengolahan Ikan \u0026 Biota Air (Bukan Udang) dalam Kaleng \nC10222 - Industri Pengolahan Udang dalam Kaleng "}, {"color": "#F8AD3C", "id": "C1029 - Industri Pengolahan \u0026 Pengawetan Biota Air Lainnya ", "label": "Industri Pengolahan \u0026 Pengawetan\nBiota Air Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan dan Pengawetan Biota Air Lainnya \n\nSub-Industri:\nC10291 - Industri Penggaraman/Pengeringan Biota Air Lainnya\nC10292 - Industri Pengasapan/Pemanggangan Biota Air Lainnya\nC10293 - Industri Pembekuan Biota Air Lainnya\nC10294 - Industri Pemindangan Biota Air Lainnya\nC10295 - Industri Peragian/Fermentasi Biota Air Lainnya\nC10296 - Industri Berbasis Lumatan Biota Air Lainnya\nC10297 - Industri Pendinginan/Pengesan Biota Air Lainnya\nC10298 - Industri Pengolahan Rumput Laut\nC10299 - Industri Pengolahan Biota Air Lainnya "}, {"color": "#F8AD3C", "id": "C103 - Industri Pengolahan Buah \u0026 Sayuran ", "label": "Industri Pengolahan\nBuah \u0026 Sayuran \nRp28.85 Tn", "shape": "dot", "size": 10.421833814393999, "title": "Industri Pengolahan dan Pengawetan Buah-Buahan dan Sayuran "}, {"color": "#F8AD3C", "id": "C1031 - Industri Pengolahan Buah \u0026 Sayuran Diasinkan, Dilumatkan, Dikeringkan \u0026 Dibekukan", "label": "Industri Pengolahan\nBuah \u0026 Sayuran Diasinkan, Dilumatkan, Dikeringkan \u0026 Dibekukan\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan dan Pengawetan Buah-Buahan dan Sayuran Dengan Cara Diasinkan, Dilumatkan, Dikeringkan dan Dibekukan \n\nSub-Industri:\nC10311 - Industri Pengasinan Buah \u0026 Sayuran\nC10312 - Industri Pelumatan Buah \u0026 Sayuran\nC10313 - Industri Pengeringan Buah \u0026 Sayuran\nC10314 - Industri Pembekuan Buah \u0026 Sayuran"}, {"color": "#F8AD3C", "id": "C1032 - Industri Pengolahan Buah \u0026 Sayuran dalam Kaleng ", "label": "Industri Pengolahan\nBuah \u0026 Sayuran dalam Kaleng \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan dan Pengawetan Buah-Buahan dan Sayuran Dalam Kaleng "}, {"color": "#F8AD3C", "id": "C1033 - Industri Pengolahan Sari Buah \u0026 Sayuran", "label": "Industri Pengolahan\nSari Buah \u0026 Sayuran\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan Sari Buah dan Sayuran"}, {"color": "#F8AD3C", "id": "C1039 - Industri Pengolahan Buah \u0026 Sayuran Lainnya", "label": "Industri Pengolahan\nBuah \u0026 Sayuran Lainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan dan Pengawetan Lainnya Buah-Buahan dan Sayuran \n\nSub-Industri:\nC10391 - Industri Tempe Kedelai\nC10392 - Industri Tahu Kedelai\nC10393 - Industri Pengolahan Kedelai \u0026 Kacang Lainnya\nC10399 - Industri Pengolahan Lainnya Buah \u0026 Sayuran Bukan Kacang"}, {"color": "#F8AD3C", "id": "C104 - Industri Minyak Nabati \u0026 Hewani", "label": "Industri Minyak Nabati\n\u0026 Hewani\nRp521.7 Tn", "shape": "dot", "size": 17.627806489777264, "title": "Industri Minyak dan Lemak Nabati dan Hewani"}, {"color": "#F8AD3C", "id": "C1041 - Industri Minyak Nabati \u0026 Hewani (Bukan Kelapa \u0026 Kelapa Sawit)", "label": "Industri Minyak Nabati \u0026 Hewani\n(Bukan Kelapa \u0026 Kelapa Sawit)\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minyak dan Lemak Nabati dan Hewani (Bukan Kelapa dan Kelapa Sawit) \n\nSub-Industri:\nC10411 - Industri Minyak Mentah \u0026 Lemak Nabati\nC10412 - Industri Margarine\nC10413 - Industri Minyak Mentah \u0026 Lemak Hewani Selain Ikan\nC10414 - Industri Minyak Ikan\nC10415 - Industri Minyak Goreng Bukan Minyak Kelapa \u0026 Kelapa Sawit"}, {"color": "#F8AD3C", "id": "C1042 - Industri Kopra, Minyak Mentah, Minyak Goreng Kelapa \u0026 Pelet Kelapa", "label": "Industri Kopra, Minyak Mentah,\nMinyak Goreng Kelapa\n\u0026 Pelet Kelapa\nRp17.79 Tn", "shape": "dot", "size": 10.260056787285352, "title": "Industri Kopra, Minyak Mentah dan Minyak Goreng Kelapa, dan Pelet Kelapa \n\nSub-Industri:\nC10421 - Industri Kopra\nC10422 - Industri Minyak Mentah Kelapa\nC10423 - Industri Minyak Goreng Kelapa\nC10424 - Industri Pelet Kelapa"}, {"color": "#F8AD3C", "id": "C1043 - Industri Minyak Mentah \u0026 Minyak Goreng Kelapa Sawit", "label": "Industri Minyak Mentah\n\u0026 Minyak Goreng\nKelapa Sawit\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minyak Mentah/Murni Kelapa Sawit (Crude Palm Oil) dan Minyak Goreng Kelapa Sawit\n\nSub-Industri:\nC10431 - Industri Minyak Mentah Kelapa Sawit\nC10432 - Industri Minyak Mentah Inti Kelapa Sawit\nC10433 - Industri Pemisahan Minyak Mentah \u0026 Minyak Mentah Inti Kelapa Sawit \nC10434 - Industri Pemurnian Minyak Mentah \u0026 Minyak Mentah Inti Kelapa Sawit\nC10435 - Industri Pemisahan Minyak Murni Kelapa Sawit \nC10436 - Industri Pemisahan Minyak Murni Inti Kelapa Sawit \nC10437 - Industri Minyak Goreng Kelapa Sawit"}, {"color": "#F8AD3C", "id": "C1049 - Industri Minyak Mentah \u0026 Lemak Nabati \u0026 Hewani Lainnya ", "label": "Industri Minyak Mentah\n\u0026 Lemak Nabati\n\u0026 Hewani Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minyak Mentah dan Lemak Nabati dan Hewani Lainnya "}, {"color": "#F8AD3C", "id": "C105 - Industri Pengolahan Susu \u0026 Es Krim", "label": "Industri Pengolahan Susu\n\u0026 Es Krim\nRp92.02 Tn", "shape": "dot", "size": 11.345370119288802, "title": "Industri Pengolahan Susu, Produk dari Susu dan Es Krim"}, {"color": "#F8AD3C", "id": "C1051 - Industri Pengolahan Susu Bubuk \u0026 Susu Kental ", "label": "Industri Pengolahan Susu\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan Susu Segar dan Krim"}, {"color": "#F8AD3C", "id": "C1052 - Industri Pengolahan Susu Bubuk \u0026 Susu Kental ", "label": "Industri Pengolahan Susu Bubuk\n\u0026 Susu Kental \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan Susu Bubuk dan Susu Kental "}, {"color": "#F8AD3C", "id": "C1053 - Industri Pengolahan Es Krim \u0026 Sejenisnya", "label": "Industri Pengolahan Es Krim\n\u0026 Sejenisnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan Es Krim dan Sejenisnya\nC10531 - Industri Pengolahan Es Krim\nC10532 - Industri Pengolahan Es Krim Sejenisnya"}, {"color": "#F8AD3C", "id": "C1059 - Industri Pengolahan Produk dari Susu Lainnya ", "label": "Industri Pengolahan Produk\ndari Susu Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengolahan Produk dari Susu Lainnya "}, {"color": "#F8AD3C", "id": "C106 - Industri Penggilingan Padi-Padian, Tepung \u0026 Pati", "label": "Industri Penggilingan Padi-Padian,\nTepung \u0026 Pati\nRp412.59 Tn", "shape": "dot", "size": 16.03255351134793, "title": "Industri Penggilingan Padi-Padian, Tepung dan Pati"}, {"color": "#F8AD3C", "id": "C1061 - Industri Penggilingan Serelia \u0026 Biji-Bijian Lainnya", "label": "Industri Penggilingan Serelia\n\u0026 Biji-Bijian Lainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Penggilingan Serelia dan Biji-Bijian Lainnya (Bukan Beras dan Jagung) \n\nSub-Industri:\nC10611 - Industri Penggilingan Gandum \u0026 Serelia Lainnya\nC10612 - Industri Penggilingan Aneka Kacang\nC10613 - Industri Penggilingan Aneka Umbi \u0026 Sayuran\nC10614 - Industri Tepung Campuran \u0026 Adonan Tepung\nC10615 - Industri Makanan Sereal\nC10616 - Industri Tepung Terigu"}, {"color": "#F8AD3C", "id": "C1062 - Industri Pati", "label": "Industri Pati\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pati dan Produk Pati (Bukan Beras dan Jagung) \n\nSub-Industri:\nC10621 - Industri Pati Ubi Kayu\nC10622 - Industri Berbagai Macam Pati Palma\nC10623 - Industri Glukosa \u0026 Sejenisnya\nC10629 - Industri Pati \u0026 Produk Pati Lainnya"}, {"color": "#F8AD3C", "id": "C1063 - Industri Penggilingan dan Industri Tepung Beras \u0026 Jagung ", "label": "Industri Penggilingan\ndan Industri Tepung Beras\n\u0026 Jagung \nRp316.85 Tn", "shape": "dot", "size": 14.632598084572116, "title": "Industri Penggilingan Beras dan Jagung dan Industri Tepung Beras dan Jagung \n\nSub-Industri:\nC10631 - Industri Penggilingan Padi \u0026 Penyosohan Beras \nC10632 - Industri Penggilingan \u0026 Pembersihan Jagung\nC10633 - Industri Tepung Beras \u0026 Jagung\nC10634 - Industri Pati Beras \u0026 Jagung\nC10635 - Industri Pemanis dari Beras \u0026 Jagung\nC10636 - Industri Minyak dari Jagung \u0026 Beras"}, {"color": "#F8AD3C", "id": "C107 - Industri Makanan Lainnya", "label": "Industri Makanan Lainnya\nRp538.92 Tn", "shape": "dot", "size": 17.879515615536533, "title": "Industri Makanan Lainnya"}, {"color": "#F8AD3C", "id": "C1071 - Industri Produk Roti \u0026 Kue", "label": "Industri Produk Roti \u0026 Kue\nRp72.03 Tn", "shape": "dot", "size": 11.053090568393351, "title": "Industri Produk Roti dan Kue"}, {"color": "#F8AD3C", "id": "C1072 - Industri Gula ", "label": "Industri Gula \nRp86.97 Tn", "shape": "dot", "size": 11.271642530972679, "title": "Industri Gula \n\nSub-Industri:\nC10721 - Industri Gula Pasir\nC10722 - Industri Gula Merah\nC10723 - Industri Sirop\nC10729 - Industri Pengolahan Gula Lainnya Bukan Sirop"}, {"color": "#F8AD3C", "id": "C1073 - Industri Kakao, Cokelat \u0026 Kembang Gula ", "label": "Industri Kakao, Cokelat\n\u0026 Kembang Gula \nRp38.51 Tn", "shape": "dot", "size": 10.563079242967992, "title": "Industri Kakao, Cokelat dan Kembang Gula \n\nSub-Industri:\nC10731 - Industri Kakao\nC10732 - Industri Makanan dari Cokelat \u0026 Kembang Gula dari Coklat \nC10733 - Industri Manisan Buah \u0026 Sayuran Kering\nC10734 - Industri Kembang Gula\nC10739 - Industri Kembang Gula Lainnya"}, {"color": "#F8AD3C", "id": "C1074 - Industri Makaroni, Mie \u0026 Sejenisnya ", "label": "Industri Makaroni, Mie\n\u0026 Sejenisnya \nRp41.06 Tn", "shape": "dot", "size": 10.60030519956808, "title": "Industri Makaroni, Mie dan Produk Sejenisnya "}, {"color": "#F8AD3C", "id": "C1075 - Industri Makanan \u0026 Masakan Olahan", "label": "Industri Makanan\n\u0026 Masakan Olahan\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Makanan dan Masakan Olahan"}, {"color": "#F8AD3C", "id": "C1076 - Industri Pengolahan Kopi, Teh \u0026 Herbal", "label": "Industri Pengolahan Kopi,\nTeh \u0026 Herbal\nRp55.32 Tn", "shape": "dot", "size": 10.808777978153751, "title": "Industri Pengolahan Kopi, Teh dan Herbal (Herb Infusion) \n\nSub-Industri\nC10761 - Industri Pengolahan Kopi\nC10762 - Industri Pengolahan Herbal\nC10763 - Industri Pengolahan The"}, {"color": "#F8AD3C", "id": "C1077 - Industri Bumbu \u0026 Produk Masak Lainnya ", "label": "Industri Bumbu\n\u0026 Produk Masak Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Bumbu-Bumbuan dan Produk Masak Lainnya \n\nSub-Industri:\nC10771 - Industri Kecap\nC10772 - Industri Bumbu \u0026 Penyedap Masakan\nC10773 - Industri Produk Masak dari Kelapa\nC10774 - Industri Pengolahan Garam\nC10779 - Industri Produk Masak Lainnya"}, {"color": "#F8AD3C", "id": "C1079 - Industri Produk Makanan Lainnya", "label": "Industri Produk Makanan Lainnya\nRp175.03 Tn", "shape": "dot", "size": 12.559060957342918, "title": "Industri Produk Makanan Lainnya\n\nSub-Industri:\nC10791 - Industri Makanan Bayi\nC10792 - Industri Kue Basah\nC10793 - Industri Makanan dari Kedele \u0026 Kacang-Kacangan Lainnya\nC10794 - Industri Kerupuk, Keripik, Peyek \u0026 Sejenisnya\nC10795 - Industri Krimer Nabati\nC10796 - Industri Dodol\nC10799 - Industri Produk Makanan Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C108 - Industri Makanan Hewan", "label": "Industri Makanan Hewan\nRp99.19 Tn", "shape": "dot", "size": 11.450264629527965, "title": "Industri Makanan Hewan\n\nSub-Industri:\nC10801 - Industri Ransum Makanan Hewan\nC10802 - Industri Konsentrat Makanan Hewan"}, {"color": "#F8AD3C", "id": "C11 - Industri Minuman", "label": "Industri Minuman\nRp75.02 Tn", "physics": false, "shape": "dot", "size": 11.096886066889942, "title": "Industri Minuman", "x": 627.8517785456737, "y": -168.2323770140604}, {"color": "#F8AD3C", "id": "C1101 - Industri Minuman Beralkohol Hasil Destilasi ", "label": "Industri Minuman Beralkohol\nHasil Destilasi \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minuman Beralkohol Hasil Destilasi "}, {"color": "#F8AD3C", "id": "C1102 - Industri Minuman Beralkohol Hasil Fermentasi Anggur", "label": "Industri Minuman Beralkohol\nHasil Fermentasi Anggur\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minuman Beralkohol Hasil Fermentasi Anggur dan Hasil Pertanian Lainnya "}, {"color": "#F8AD3C", "id": "C1103 - Industri Minuman Beralkohol Hasil Fermentasi Malt \u0026 Industri Malt ", "label": "Industri Minuman Beralkohol\nHasil Fermentasi Malt \u0026 Industri Malt \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minuman Beralkohol Hasil Fermentasi Malt dan Industri Malt \n\nSub-Industri:\nC11031 - Industri Minuman Beralkohol Hasil Fermentasi Malt\nC11032 - Industri Malt"}, {"color": "#F8AD3C", "id": "C1104 - Industri Minuman Ringan ", "label": "Industri Minuman Ringan \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minuman Ringan "}, {"color": "#F8AD3C", "id": "C1105 - Industri Air Kemasan \u0026 Air Minum Isi Ulang ", "label": "Industri Air Kemasan\n\u0026 Air Minum Isi Ulang \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Air Kemasan dan Air Minum Isi Ulang \n\nSub-Industri:\nC11051 - Industri Air Kemasan\nC11052 - Industri Air Minum Isi Ulang"}, {"color": "#F8AD3C", "id": "C1109 - Industri Minuman Lainnya ", "label": "Industri Minuman Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Minuman Lainnya "}, {"color": "#F8AD3C", "id": "C12 - Industri Pengolahan Tembakau", "label": "Industri Pengolahan Tembakau\nRp223.97 Tn", "physics": false, "shape": "dot", "size": 13.27466857533373, "title": "Industri Pengolahan Tembakau", "x": 562.9165034450998, "y": -324.99999669080745}, {"color": "#F8AD3C", "id": "C1201 - Industri Rokok \u0026 Produk Tembakau Lainnya ", "label": "Industri Rokok\n\u0026 Produk Tembakau Lainnya \nRp197.1 Tn", "shape": "dot", "size": 12.881817450032152, "title": "Industri Rokok dan Produk Tembakau Lainnya \n\nSub-Industri:\nC12011 - Industri Sigaret Kretek Tangan\nC12012 - Industri Rokok Putih\nC12013 - Industri Sigaret Kretek Mesin\nC12019 - Industri Rokok Lainnya"}, {"color": "#F8AD3C", "id": "C1209 - Industri Pengolahan Tembakau Lainnya ", "label": "Industri Pengolahan Tembakau\nLainnya \nRp26.87 Tn", "shape": "dot", "size": 10.392851125301577, "title": "Industri Pengolahan Tembakau Lainnya \n\nSub-Industri\nC12091 - Industri Pengeringan \u0026 Pengolahan Tembakau\nC12099 - Industri Bumbu Rokok serta Kelengkapan Rokok Lainnya"}, {"color": "#F8AD3C", "id": "C13 - Industri Tekstil", "label": "Industri Tekstil\nRp274.76 Tn", "physics": false, "shape": "dot", "size": 14.017340868954307, "title": "Industri Tekstil", "x": 459.619402285021, "y": -459.6193949132282}, {"color": "#F8AD3C", "id": "C131 - Industri Pemintalan, Pertenunan \u0026 Penyempurnaan Tekstil ", "label": "Industri Pemintalan, Pertenunan\n\u0026 Penyempurnaan Tekstil \nRp188.76 Tn", "shape": "dot", "size": 12.759916715563307, "title": "Industri Pemintalan, Pertenunan dan Penyempurnaan Tekstil "}, {"color": "#F8AD3C", "id": "C1311 - Industri Persiapan \u0026 Pemintalan Serat Tekstil ", "label": "Industri Persiapan\n\u0026 Pemintalan Serat Tekstil \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Persiapan dan Pemintalan Serat Tekstil \n\nSub-Industri:\nC13111 - Industri Persiapan Serat Tekstil\nC13112 - Industri Pemintalan Benang\nC13113 - Industri Pemintalan Benang Jahit "}, {"color": "#F8AD3C", "id": "C1312 - Industri Pertenunan Tekstil ", "label": "Industri Pertenunan Tekstil \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pertenunan Tekstil "}, {"color": "#F8AD3C", "id": "C1313 - Industri Penyempurnaan Tekstil", "label": "Industri Penyempurnaan Tekstil\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Penyempurnaan Tekstil\n\nSub-Industri:\nC13131 - Industri Penyempurnaan Benang \nC13132 - Industri Penyempurnaan Kain \nC13133 - Industri Pencetakan Kain \nC13134 - Industri Batik "}, {"color": "#F8AD3C", "id": "C139 - Industri Tekstil Lainnya ", "label": "Industri Tekstil Lainnya \nRp86.0 Tn", "shape": "dot", "size": 11.257424153391, "title": "Industri Tekstil Lainnya "}, {"color": "#F8AD3C", "id": "C1391 - Industri Kain Rajutan \u0026 Sulaman ", "label": "Industri Kain Rajutan \u0026 Sulaman \nRp37.64 Tn", "shape": "dot", "size": 10.550290303145989, "title": "Industri Kain Rajutan dan Sulaman \n\nSub-Industri:\nC13911 - Industri Kain Rajutan \nC13912 - Industri Kain Sulaman \nC13913 - Industri Bulu Tiruan Rajutan "}, {"color": "#F8AD3C", "id": "C1392 - Industri Pembuatan Barang Tekstil, Bukan Pakaian Jadi ", "label": "Industri Pembuatan Barang Tekstil,\nBukan Pakaian Jadi \nRp38.08 Tn", "shape": "dot", "size": 10.556738081263031, "title": "Industri Pembuatan Barang Tekstil, Bukan Pakaian Jadi \n\nSub-Industri:\nC13921 - Industri Barang Jadi Tekstil untuk Keperluan Rumah Tangga \nC13922 - Industri Barang Jadi Tekstil Sulaman\nC13923 - Industri Bantal \u0026 Sejenisnya\nC13924 - Industri Barang Jadi Rajutan \u0026 Sulaman \nC13925 - Industri Karung Goni \nC13926 - Industri Karung Bukan Goni \nC13929 - Industri Barang Jadi Tekstil Lainnya "}, {"color": "#F8AD3C", "id": "C1393 - Industri Karpet \u0026 Permadani ", "label": "Industri Karpet \u0026 Permadani \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Karpet dan Permadani "}, {"color": "#F8AD3C", "id": "C1394 - Industri Tali \u0026 Barang dari Tali", "label": "Industri Tali\n\u0026 Barang dari Tali\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Tali dan Barang dari Tali\n\nSub-Industri:\nC13941 - Industri Tali\nC13942 - Industri Barang dari Tali"}, {"color": "#F8AD3C", "id": "C1399 - Industri Tekstil Lainnya Ytdl", "label": "Industri Tekstil Lainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Tekstil Lainnya Ytdl\n\nSub-Industri:\nC13991 - Industri Kain Pita\nC13992 - Industri Kain Keperluan Industri \nC13993 - Industri Non Woven\nC13994 - Industri Kain Ban \nC13995 - Industri Kapuk \nC13996 - Industri Kain Tulle \u0026 Kain Jaring \nC13999 - Industri Tekstil Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C14 - Industri Pakaian Jadi ", "label": "Industri Pakaian Jadi \nRp236.7 Tn", "physics": false, "shape": "dot", "size": 13.460859839685352, "title": "Industri Pakaian Jadi ", "x": 324.99998469109096, "y": -562.9165348163256}, {"color": "#F8AD3C", "id": "C141 - Industri Pakaian Jadi \u0026 Perlengkapannya", "label": "Industri Pakaian Jadi\n\u0026 Perlengkapannya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pakaian Jadi dan Perlengkapannya, Bukan Pakaian Jadi dari Kulit Berbulu"}, {"color": "#F8AD3C", "id": "C1411 - Industri Pakaian Jadi (Bukan Penjahitan \u0026 Pembuatan Pakaian) ", "label": "Industri Pakaian Jadi\n(Bukan Penjahitan \u0026 Pembuatan Pakaian) \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pakaian Jadi (Bukan Penjahitan dan Pembuatan Pakaian) \n\nSub-Industri:\nC14111 - Industri Pakaian Jadi (Konveksi) dari Tekstil\nC14112 - Industri Pakaian Jadi (Konveksi) dari Kulit"}, {"color": "#F8AD3C", "id": "C1412 - Penjahitan \u0026 Pembuatan Pakaian Sesuai Pesanan ", "label": "Penjahitan \u0026 Pembuatan\nPakaian Sesuai Pesanan \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Penjahitan dan Pembuatan Pakaian Sesuai Pesanan "}, {"color": "#F8AD3C", "id": "C1413 - Industri Perlengkapan Pakaian", "label": "Industri Perlengkapan Pakaian\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Perlengkapan Pakaian yang Utamanya Terbuat dari Tekstil \n\nSub-Industri:\nC14131 - Industri Perlengkapan Pakaian dari Tekstil\nC14132 - Industri Perlengkapan Pakaian dari Kulit"}, {"color": "#F8AD3C", "id": "C142 - Industri Pakaian Jadi \u0026 Barang dari Kulit Berbulu", "label": "Industri Pakaian Jadi \u0026 Barang\ndari Kulit Berbulu\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pakaian Jadi dan Barang dari Kulit Berbulu"}, {"color": "#F8AD3C", "id": "C143 - Industri Pakaian Jadi Rajutan \u0026 Sulaman/Bordir", "label": "Industri Pakaian Jadi Rajutan\n\u0026 Sulaman/Bordir\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pakaian Jadi Rajutan dan Sulaman/Bordir\n\nSub-Industri:\nC14301 - Industri Pakaian Jadi Rajutan\nC14302 - Industri Pakaian Jadi Sulaman/Bordir\nC14303 - Industri Rajutan Kaos Kaki \u0026 Sejenisnya"}, {"color": "#F8AD3C", "id": "C15 - Industri Kulit, Barang dari Kulit \u0026 Alas Kaki", "label": "Industri Kulit, Barang dari Kulit\n\u0026 Alas Kaki\nRp141.16 Tn", "physics": false, "shape": "dot", "size": 12.063951387735177, "title": "Industri Kulit, Barang dari Kulit dan Alas Kaki", "x": 168.23240375736256, "y": -627.8517711738808}, {"color": "#F8AD3C", "id": "C151 - Industri Kulit \u0026 Barang dari Kulit", "label": "Industri Kulit \u0026 Barang dari Kulit\nRp46.95 Tn", "shape": "dot", "size": 10.686435060449776, "title": "Industri Kulit dan Barang dari Kulit, Termasuk Kulit Buatan "}, {"color": "#F8AD3C", "id": "C1511 - Industri Kulit \u0026 Kulit Komposisi", "label": "Industri Kulit \u0026 Kulit Komposisi\nRp26.16 Tn", "shape": "dot", "size": 10.382511204819076, "title": "Industri Kulit dan Kulit Komposisi, Termasuk Pencelupan Kulit Berbulu\n\nSub-Industri:\nC15111 - Industri Pengawetan Kulit \nC15112 - Industri Penyamakan Kulit \nC15113 - Industri Pencelupan Kulit Bulu \nC15114 - Industri Kulit Komposisi "}, {"color": "#F8AD3C", "id": "C1512 - Industri Barang dari Kulit, Koper, Tas Tangan \u0026 Sejenisnya", "label": "Industri Barang dari Kulit,\nKoper, Tas Tangan\n\u0026 Sejenisnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Kulit dan Kulit Komposisi, Koper, Tas Tangan dan Sejenisnya, Pelana dan Alat Pengekang (Harness)\n\nSub-Industri:\nC15121 - Industri Barang dari Kulit untuk Keperluan Pribadi\nC15122 - Industri Barang dari Kulit untuk Keperluan Teknik/Industri\nC15123 - Industri Barang dari Kulit untuk Keperluan Hewan\nC15129 - Industri Barang dari Kulit untuk Keperluan Lainnya "}, {"color": "#F8AD3C", "id": "C152 - Industri Alas Kaki", "label": "Industri Alas Kaki\nRp94.21 Tn", "shape": "dot", "size": 11.3775163272854, "title": "Industri Alas Kaki\n\nSub-Industri:\nC15201 - Industri Alas Kaki Sehari-Hari\nC15202 - Industri Sepatu Olahraga\nC15203 - Industri Sepatu Teknik Lapangan/Keperluan Industri\nC15209 - Industri Alas Kaki Lainnya"}, {"color": "#F8AD3C", "id": "C16 - Industri Kayu, Gabus, Bambu, Rotan \u0026 Sejenisnya ", "label": "Industri Kayu, Gabus,\nBambu, Rotan \u0026 Sejenisnya \nRp192.41 Tn", "physics": false, "shape": "dot", "size": 12.813201995858844, "title": "Industri Kayu, Barang dari Kayu dan Gabus (Tidak Termasuk Furnitur) dan Barang Anyaman dari Bambu, Rotan dan Sejenisnya ", "x": -2.0287201468642947e-05, "y": -649.9999926282071}, {"color": "#F8AD3C", "id": "C161 - Industri Penggergajian Kayu, Rotan, Bambu \u0026 Sejenisnya ", "label": "Industri Penggergajian\nKayu, Rotan, Bambu \u0026 Sejenisnya \nRp43.32 Tn", "shape": "dot", "size": 10.633363758649962, "title": "Industri Penggergajian dan Pengawetan Kayu, Rotan, Bambu dan Sejenisnya \nC16101 - Industri Penggergajian Kayu\nC16102 - Industri Pengawetan Kayu\nC16103 - Industri Pengawetan Rotan, Bambu \u0026 Sejenisnya\nC16104 - Industri Pengolahan Rotan\nC16105 - Industri Partikel Kayu \u0026 Sejenisnya"}, {"color": "#F8AD3C", "id": "C162 - Industri Barang dari Kayu \u0026 Sejenis Lainnya", "label": "Industri Barang dari Kayu\n\u0026 Sejenis Lainnya\nRp149.09 Tn", "shape": "dot", "size": 12.179838237208882, "title": "Industri Barang dari Kayu; Industri Barang dari Gabus dan Barang Anyaman dari Jerami, Rotan, Bambu dan Sejenis Lainnya"}, {"color": "#F8AD3C", "id": "C1621 - Industri Veneer, Kayu Lapis, Kayu Laminasi \u0026 Sejenisnya ", "label": "Industri Veneer, Kayu Lapis,\nKayu Laminasi \u0026 Sejenisnya \nRp82.16 Tn", "shape": "dot", "size": 11.201296753254695, "title": "Industri Veneer, Kayu Lapis, Kayu Laminasi dan Sejenisnya \n\nSub-Industri:\nC16211 - Industri Kayu Lapis \nC16212 - Industri Kayu Lapis Laminasi\nC16213 - Industri Panel Kayu Lainnya \nC16214 - Industri Veneer \nC16215 - Industri Kayu Laminasi"}, {"color": "#F8AD3C", "id": "C1622 - Industri Barang Bangunan dari Kayu", "label": "Industri Barang Bangunan\ndari Kayu\nRp51.16 Tn", "shape": "dot", "size": 10.747972351617546, "title": "Industri Barang Bangunan dari Kayu\nC16221 - Industri Barang Bangunan dari Kayu\nC16222 - Industri Bangunan Prafabrikasi dari Kayu"}, {"color": "#F8AD3C", "id": "C1623 - Industri Wadah dari Kayu ", "label": "Industri Wadah dari Kayu \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Wadah dari Kayu "}, {"color": "#F8AD3C", "id": "C1629 - ndustri Barang Lainnya dari Kayu \u0026 Sejenisnya ", "label": "Industri Barang Lainnya\ndari Kayu \u0026 Sejenisnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang Lainnya dari Kayu; Industri Barang dari Gabus dan Barang Anyaman dari Jerami, Rotan, Bambu dan Sejenisnya \n\nSub-Industri:\nC16291 - Industri Barang Anyaman dari Rotan \u0026 Bambu \nC16292 - Industri Barang Anyaman dari Tanaman Bukan Rotan \u0026 Bambu \nC16293 - Industri Kerajinan Ukiran dari Kayu\nC16294 - Industri Alat Dapur dari Kayu, Rotan \u0026 Bambu \nC16295 - Industri Kayu Bakar \u0026 Pelet Kayu \nC16299 - Industri Barang dari Kayu, Rotan, Gabus Lainnya Ytdl "}, {"color": "#F8AD3C", "id": "C17 - Industri Kertas \u0026 Barang dari Kertas", "label": "Industri Kertas \u0026 Barang dari Kertas\nRp243.69 Tn", "physics": false, "shape": "dot", "size": 13.562949104273443, "title": "Industri Kertas dan Barang dari Kertas", "x": -168.23234876394278, "y": -627.8517711738808}, {"color": "#F8AD3C", "id": "C1701 - Industri Bubur Kertas, Kertas \u0026 Papan Kertas ", "label": "Industri Bubur Kertas, Kertas\n\u0026 Papan Kertas \nRp49.93 Tn", "shape": "dot", "size": 10.730080839793908, "title": "Industri Bubur Kertas, Kertas dan Papan Kertas \n\nSub-Industri:\nC17011 - Industri Bubur Kertas\nC17012 - Industri Kertas Budaya\nC17013 - Industri Kertas Berharga\nC17014 - Industri Kertas Khusus\nC17019 - Industri Kertas Lainnya"}, {"color": "#F8AD3C", "id": "C1702 - Industri Kertas, Papan Kertas \u0026 Wadah dari Kertas", "label": "Industri Kertas, Papan Kertas\n\u0026 Wadah dari Kertas\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Kertas dan Papan Kertas Bergelombang dan Wadah dari Kertas dan Papan Kertas \n\nSub-Industri:\nC17021 - Industri Kertas \u0026 Papan Kertas Bergelombang\nC17022 - Industri Kemasan \u0026 Kotak dari Kertas \u0026 Karton"}, {"color": "#F8AD3C", "id": "C1709 - Industri Barang dari Kertas \u0026 Papan Kertas Lainnya ", "label": "Industri Barang dari Kertas\n\u0026 Papan Kertas Lainnya \nRp101.01 Tn", "shape": "dot", "size": 11.47681033237292, "title": "Industri Barang dari Kertas dan Papan Kertas Lainnya \n\nSub-Industri:\nC17091 - Industri Kertas Tissue\nC17099 - Industri Barang dari Kertas \u0026 Papan Kertas Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C18 -  Industri Pencetakan \u0026 Reproduksi Media Rekaman", "label": "Industri Pencetakan\n\u0026 Reproduksi Media Rekaman\nRp40.14 Tn", "physics": false, "shape": "dot", "size": 10.586857495475268, "title": "Industri Pencetakan dan Reproduksi Media Rekaman", "x": -325.0000265552178, "y": -562.916496073307}, {"color": "#F8AD3C", "id": "C181 - Industri Pencetakan \u0026 Kegiatan Ybdi", "label": "Industri Pencetakan\n\u0026 Kegiatan Ybdi\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pencetakan dan Kegiatan Ybdi"}, {"color": "#F8AD3C", "id": "C1811 - Industri Pencetakan ", "label": "Industri Pencetakan \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pencetakan \n\nSub-Industri:\nC18111 - Industri Pencetakan Umum\nC18112 - Industri Pencetakan Khusus\nC18113 - Industri Pencetakan 3D Printing"}, {"color": "#F8AD3C", "id": "C1812 - Kegiatan Jasa Penunjang Pencetakan ", "label": "Kegiatan Jasa\nPenunjang Pencetakan \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Kegiatan Jasa Penunjang Pencetakan "}, {"color": "#F8AD3C", "id": "C182 - Reproduksi Media Rekaman", "label": "Reproduksi Media Rekaman\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reproduksi Media Rekaman\n\nSub-Industri:\nC18201 - Reproduksi Media Rekaman Suara \u0026 Piranti Lunak\nC18202 - Reproduksi Media Rekaman Film \u0026 Video"}, {"color": "#F8AD3C", "id": "C19 - Industri Produk dari Batu Bara \u0026 Pengilangan Minyak Bumi ", "label": "Industri Produk dari Batu Bara\n\u0026 Pengilangan Minyak Bumi \nRp0.0 Tn", "physics": false, "shape": "dot", "size": 3.0, "title": "Industri Produk dari Batu Bara dan Pengilangan Minyak Bumi ", "x": -459.61938603461994, "y": -459.6193949132282}, {"color": "#F8AD3C", "id": "C191 - Industri Produk dari Batu Bara", "label": "Industri Produk dari Batu Bara\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Produk dari Batu Bara"}, {"color": "#F8AD3C", "id": "C192 - Industri Produk Pengilangan Minyak Bumi", "label": "Industri Produk Pengilangan\nMinyak Bumi\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Produk Pengilangan Minyak Bumi"}, {"color": "#F8AD3C", "id": "C1921 - Industri Bahan Bakar \u0026 Minyak Pelumas", "label": "Industri Bahan Bakar\n\u0026 Minyak Pelumas\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Bahan Bakar dan Minyak Pelumas Hasil Pengilangan Minyak Bumi\n\nSub-Industri:\nC19211 - Industri Bahan Bakar dari Pemurnian \u0026 Pengilangan Minyak Bumi \nC19212 - Industri Pembuatan Minyak Pelumas\nC19213 - Industri Pengolahan Kembali Minyak Pelumas Bekas \nC19214 - Industri Pengolahan Minyak Pelumas Bekas Menjadi Bahan Bakar "}, {"color": "#F8AD3C", "id": "C1929 - Industri Produk Pengilangan Minyak Bumi Lainnya ", "label": "Industri Produk Pengilangan\nMinyak Bumi Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Produk Pengilangan Minyak Bumi Lainnya \n\nSub-Industri:\nC19291 - Industri Produk dari Hasil Kilang Minyak Bumi \nC19292 - Industri Briket Batu Bara"}, {"color": "#F8AD3C", "id": "C20 - Industri Bahan Kimia \u0026 Barang dari Bahan Kimia", "label": "Industri Bahan Kimia\n\u0026 Barang dari Bahan Kimia\nRp860.96 Tn", "physics": false, "shape": "dot", "size": 22.58808197297879, "title": "Industri Bahan Kimia dan Barang dari Bahan Kimia", "x": -562.9164871946987, "y": -325.00003543382604}, {"color": "#F8AD3C", "id": "C201 - Industri Bahan Kimia", "label": "Industri Bahan Kimia\nRp570.73 Tn", "shape": "dot", "size": 18.344647960263842, "title": "Industri Bahan Kimia"}, {"color": "#F8AD3C", "id": "C2011 - Industri Kimia Dasar", "label": "Industri Kimia Dasar\nRp356.68 Tn", "shape": "dot", "size": 15.215003964696695, "title": "Industri Kimia Dasar\n\nSub-Industri:\nC20111 - Industri Kimia Dasar Anorganik Khlor \u0026 Alkali\nC20112 - Industri Kimia Dasar Anorganik Gas Industri\nC20113 - Industri Kimia Dasar Anorganik Pigmen\nC20114 - Industri Kimia Dasar Anorganik Lainnya\nC20115 - Industri Kimia Dasar Organik yang Bersumber dari Hasil Pertanian \nC20116 - Industri Kimia Dasar Organik untuk Zat Warna \u0026 Pigmen\nC20117 - Industri Kimia Dasar Organik dari Minyak Bumi, Gas Alam \u0026 Batu Bara \nC20118 - Industri Kimia Dasar Organik yang Menghasilkan Bahan Kimia Khusus \nC20119 - Industri Kimia Dasar Organik Lainnya"}, {"color": "#F8AD3C", "id": "C2012 - Industri Pupuk \u0026 Bahan Senyawa Nitrogen ", "label": "Industri Pupuk \u0026 Bahan\nSenyawa Nitrogen \nRp92.1 Tn", "shape": "dot", "size": 11.34655124841897, "title": "Industri Pupuk dan Bahan Senyawa Nitrogen \nC20121 - Industri Pupuk Alam/Non Sintetis Hara Makro Primer\nC20122 - Industri Pupuk Buatan Tunggal Hara Makro Primer\nC20123 - Industri Pupuk Buatan Majemuk Hara Makro Primer\nC20124 - Industri Pupuk Buatan Campuran Hara Makro Primer\nC20125 - Industri Pupuk Hara Makro Sekunder\nC20126 - Industri Pupuk Hara Mikro\nC20127 - Industri Pupuk Pelengkap\nC20128 - Industri Media Tanam\nC20129 - Industri Pupuk Lainnya"}, {"color": "#F8AD3C", "id": "C2013 - Industri Plastik \u0026 Karet Buatan dalam Bentuk Dasar ", "label": "Industri Plastik \u0026 Karet Buatan\ndalam Bentuk Dasar \nRp121.95 Tn", "shape": "dot", "size": 11.783092747148176, "title": "Industri Plastik dan Karet Buatan Dalam Bentuk Dasar \n\nSub-Industri:\nC20131 - Industri Damar Buatan \u0026 Bahan Baku Plastik \nC20132 - Industri Karet Buatan"}, {"color": "#F8AD3C", "id": "C202 - Industri Barang Kimia Lainnya", "label": "Industri Barang Kimia Lainnya\nRp290.23 Tn", "shape": "dot", "size": 14.243434012714951, "title": "Industri Barang Kimia Lainnya"}, {"color": "#F8AD3C", "id": "C2021 - Industri Pestisida \u0026 Produk Agrokimia Lainnya ", "label": "Industri Pestisida\n\u0026 Produk Agrokimia Lainnya \nRp24.53 Tn", "shape": "dot", "size": 10.35858821892409, "title": "Industri Pestisida dan Produk Agrokimia Lainnya \n\nSub-Industri:\nC20211 - Industri Bahan Baku Pemberantas Hama\nC20212 - Industri Pemberantas Hama\nC20213 - Industri Zat Pengatur Tumbuh\nC20214 - Industri Bahan Amelioran Pembenah Tanah"}, {"color": "#F8AD3C", "id": "C2022 - Industri Cat \u0026 Tinta Cetak, Pernis \u0026 Bahan Pelapisan Sejenisnya \u0026 Lak ", "label": "Industri Cat \u0026 Tinta Cetak, Pernis\n\u0026 Bahan Pelapisan Sejenisnya \u0026 Lak \nRp46.2 Tn", "shape": "dot", "size": 10.675543883990215, "title": "Industri Cat dan Tinta Cetak, Pernis dan Bahan Pelapisan Sejenisnya dan Lak \n\nSub-Industri:\nC20221 - Industri Cat \u0026 Tinta Cetak\nC20222 - Industri Pernis\nC20223 - Industri Lak"}, {"color": "#F8AD3C", "id": "C2023 - Industri Sabun \u0026 Deterjen, Bahan Pembersih \u0026 Pengilap, Parfum \u0026 Kosmetik ", "label": "Industri Sabun \u0026 Deterjen,\nBahan Pembersih \u0026 Pengilap,\nParfum \u0026 Kosmetik \nRp149.27 Tn", "shape": "dot", "size": 12.182537056521014, "title": "Industri Sabun dan Deterjen, Bahan Pembersih dan Pengilap, Parfum dan Kosmetik \n\nSub-Industri:\nC20231 - Industri Sabun \u0026 Bahan Pembersih Keperluan Rumah Tangga \nC20232 - Industri Kosmetik untuk Manusia, Termasuk Pasta Gigi\nC20233 - Industri Kosmetik untuk Hewan\nC20234 - Industri Perekat Gigi"}, {"color": "#F8AD3C", "id": "C2029 - Industri Barang Kimia Lainnya Ytdl ", "label": "Industri Barang Kimia Lainnya Ytdl \nRp70.23 Tn", "shape": "dot", "size": 11.026764853279632, "title": "Industri Barang Kimia Lainnya Ytdl \n\nSub-Industri:\nC20291 - Industri Perekat/Lem\nC20292 - Industri Bahan Peledak\nC20293 - Industri Tinta\nC20294 - Industri Minyak Atsiri\nC20295 - Industri Korek Api\nC20296 - Industri Minyak Atsiri Rantai Tengah\nC20299 - Industri Barang Kimia Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C203 - Industri Serat Buatan", "label": "Industri Serat Buatan\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Serat Buatan\n\nSub-Industri:\nC20301 - Industri Serat/Benang/Strip Filamen Buatan\nC20302 - Industri Serat Stapel Buatan"}, {"color": "#F8AD3C", "id": "C21 - Industri Farmasi, Produk Obat Kimia \u0026 Obat Tradisional", "label": "Industri Farmasi, Produk Obat Kimia\n\u0026 Obat Tradisional\nRp68.01 Tn", "physics": false, "shape": "dot", "size": 10.994338243534418, "title": "Industri Farmasi, Produk Obat Kimia dan Obat Tradisional", "x": -627.8517622952726, "y": -168.23243512858832}, {"color": "#F8AD3C", "id": "C2101 - Industri Farmasi \u0026 Produk Obat Kimia ", "label": "Industri Farmasi \u0026 Produk Obat Kimia \nRp54.68 Tn", "shape": "dot", "size": 10.799511386087447, "title": "Industri Farmasi dan Produk Obat Kimia \n\nSub-Industri:\nC21011 - Industri Bahan Farmasi untuk Manusia\nC21012 - Industri Produk Farmasi untuk Manusia\nC12013 - Industri Produk Farmasi untuk Hewan\nC21014 - Industri Bahan Farmasi untuk Hewan"}, {"color": "#F8AD3C", "id": "C2102 - Industri Obat Tradisional ", "label": "Industri Obat Tradisional \nRp13.33 Tn", "shape": "dot", "size": 10.194826857446973, "title": "Industri Obat Tradisional \n\nSub-Industri:\nC21021 - Industri Bahan Baku Obat Tradisional untuk Manusia\nC21022 - Industri Produk Obat Tradisional untuk Manusia\nC21023 - Industri Produk Obat Tradisional untuk Hewan\nC21024 - Industri Bahan Baku Obat Tradisional untuk Hewan"}, {"color": "#F8AD3C", "id": "C22 - Industri Karet, Barang dari Karet \u0026 Plastik", "label": "Industri Karet, Barang\ndari Karet \u0026 Plastik\nRp309.56 Tn", "physics": false, "shape": "dot", "size": 14.526090087287034, "title": "Industri Karet, Barang dari Karet dan Plastik", "x": -649.9999837495989, "y": 5.6071396335651404e-05}, {"color": "#F8AD3C", "id": "C221 - Industri Karet \u0026 Barang dari Karet", "label": "Industri Karet \u0026 Barang dari Karet\nRp138.72 Tn", "shape": "dot", "size": 12.028154181707247, "title": "Industri Karet dan Barang dari Karet"}, {"color": "#F8AD3C", "id": "C2211 - Industri Ban \u0026 Vulkanisir Ban ", "label": "Industri Ban \u0026 Vulkanisir Ban \nRp54.65 Tn", "shape": "dot", "size": 10.79904484424802, "title": "Industri Ban dan Vulkanisir Ban \n\nSub-Industri:\nC22111 - Industri Ban Luar \u0026 Ban dalam \nC22112 - Industri Vulkanisir Ban "}, {"color": "#F8AD3C", "id": "C2212 - Industri Pengasapan, Remilling \u0026 Karet Remah ", "label": "Industri Pengasapan, Remilling\n\u0026 Karet Remah \nRp70.18 Tn", "shape": "dot", "size": 11.026128152114394, "title": "Industri Pengasapan, Remilling dan Karet Remah \n\nSub-Industri:\nC22122 - Industri Pengasapan Karet \nC22122 - Industri Remilling Karet\nC22123 - Industri Karet Remah (Crumb Rubber)"}, {"color": "#F8AD3C", "id": "C2219 - Industri Barang dari Karet Lainnya ", "label": "Industri Barang dari Karet\nLainnya \nRp13.88 Tn", "shape": "dot", "size": 10.202981185344836, "title": "Industri Barang dari Karet Lainnya \n\nSub-Industri:\nC22191 - Industri Barang dari Karet untuk Keperluan Rumah Tangga \nC22192 - Industri Barang dari Karet untuk Keperluan Industri \nC22193 - Industri Barang dari Karet untuk Keperluan Infrastruktur \nC22194 - Industri Barang dari Karet untuk Kesehatan \nC22199 - Industri Barang dari Karet Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C222 - Industri Barang dari Plastik", "label": "Industri Barang dari Plastik\nRp170.85 Tn", "shape": "dot", "size": 12.497935905579787, "title": "Industri Barang dari Plastik"}, {"color": "#F8AD3C", "id": "C2221 - Industri Barang dari Plastik untuk Bangunan", "label": "Industri Barang dari Plastik\nuntuk Bangunan\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Plastik untuk Bangunan"}, {"color": "#F8AD3C", "id": "C2222 - Industri Barang dari Plastik untuk Pengemasan ", "label": "Industri Barang dari Plastik\nuntuk Pengemasan \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Plastik untuk Pengemasan "}, {"color": "#F8AD3C", "id": "C2223 - Industri Pipa Plastik \u0026 Perlengkapannya ", "label": "Industri Pipa Plastik\n\u0026 Perlengkapannya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pipa Plastik dan Perlengkapannya "}, {"color": "#F8AD3C", "id": "C2229 - Industri Barang dari Plastik Lainnya ", "label": "Industri Barang\ndari Plastik Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Plastik Lainnya \n\nSub-Industri:\nC22291 - Industri Barang Plastik Lembaran\nC22292 - Industri Perlengkapan \u0026 Peralatan Rumah Tangga\nC22293 - Industri Barang \u0026 Peralatan Teknik/Industri dari Plastik \nC22299 - Industri Barang Plastik Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C23 - Industri Barang Galian Bukan Logam", "label": "Industri Barang Galian Bukan Logam\nRp203.37 Tn", "physics": false, "shape": "dot", "size": 12.973425233509413, "title": "Industri Barang Galian Bukan Logam", "x": -627.8517622952726, "y": 168.23239487875426}, {"color": "#F8AD3C", "id": "C231 - Industri Kaca \u0026 Barang dari Kaca", "label": "Industri Kaca \u0026 Barang dari Kaca\nRp42.01 Tn", "shape": "dot", "size": 10.614230820646867, "title": "Industri Kaca dan Barang dari Kaca"}, {"color": "#F8AD3C", "id": "C2311 - Industri Kaca", "label": "Industri Kaca\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Kaca\n\nSub-Industri:\nC23111 - Industri Kaca Lembaran \nC23112 - Industri Kaca Pengaman \nC23119 - Industri Kaca Lainnya "}, {"color": "#F8AD3C", "id": "C2312 - Industri Barang dari Kaca", "label": "Industri Barang dari Kaca\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Kaca\nC23121 - Industri Perlengkapan \u0026 Peralatan Rumah Tangga dari Kaca \nC23122 - Industri Alat-Alat Laboratorium dari Kaca\nC23123 - Industri Kemasan dari Kaca \nC23124 - Industri Alat Laboratorium Klinis dari Kaca \nC23129 - Industri Barang Lainnya dari Kaca"}, {"color": "#F8AD3C", "id": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "label": "Industri Barang Galian Bukan Logam\nLainnya \nRp161.36 Tn", "shape": "dot", "size": 12.359194412862546, "title": "Industri Barang Galian Bukan Logam Lainnya "}, {"color": "#F8AD3C", "id": "C2391 - Industri Barang Refraktori", "label": "Industri Barang Refraktori\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang Refraktori (Tahan Api) \n\nSub-Industri:\nC23911 - Industri Bata, Mortar, Semen, \u0026 Sejenisnya yang Tahan Api \nC23919 - Industri Barang Tahan Api dari Tanah Liat/Keramik Lainnya "}, {"color": "#F8AD3C", "id": "C2392 - Industri Bahan Bangunan dari Tanah Liat/Keramik ", "label": "Industri Bahan Bangunan\ndari Tanah Liat/Keramik \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Bahan Bangunan dari Tanah Liat/Keramik \n\nSub-Industri:\nC23921 - Industri Batu Bata dari Tanah Liat/Keramik \nC23922 - Industri Genteng dari Tanah Liat/Keramik \nC23923 - Industri Peralatan Saniter dari Porselen \nC23929 - Industri Bahan Bangunan dari Tanah Liat/Keramik Lainnya"}, {"color": "#F8AD3C", "id": "C2393 - Industri Barang Tanah Liat/Keramik \u0026 Porselen", "label": "Industri Barang Tanah Liat/Keramik\n\u0026 Porselen\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang Tanah Liat/Keramik dan Porselen Bukan Bahan Bangunan\n\nSub-Industri:\nC23931 - Industri Perlengkapan Rumah Tangga dari Porselen \nC23932 - Industri Perlengkapan Rumah Tangga dari Tanah Liat/Keramik \nC23933 - Industri Alat Laboratorium \u0026 Alat Teknik dari Porselen \nC23939 - Industri Barang Tanah Liat/Keramik \u0026 Porselen Lainnya"}, {"color": "#F8AD3C", "id": "C2394 - Industri Semen, Kapur \u0026 Gips ", "label": "Industri Semen, Kapur \u0026 Gips \nRp88.22 Tn", "shape": "dot", "size": 11.289794413759886, "title": "Industri Semen, Kapur dan Gips \n\nSub-Industri:\nC23941 - Industri Semen \nC23942 - Industri Kapur \nC23943 - Industri Gips "}, {"color": "#F8AD3C", "id": "C2395 - Industri Barang dari Semen, Kapur, Gips \u0026 Asbes ", "label": "Industri Barang dari Semen,\nKapur, Gips \u0026 Asbes \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Semen, Kapur, Gips dan Asbes \n\nSub-Industri:\nC23951 - Industri Barang dari Semen\nC23952 - Industri Barang dari Kapur\nC23953 - Industri Barang dari Semen \u0026 Kapur untuk Konstruksi \nC23954 - Industri Barang dari Gips untuk Konstruksi \nC23955 - Industri Barang dari Asbes untuk Keperluan Bahan Bangunan \nC23956 - Industri Barang dari Asbes untuk Keperluan Industri \nC23957 - Industri Mortar atau Beton Siap Pakai \nC23959 - Industri Barang dari Semen, Kapur, Gips \u0026 Asbes Lainnya "}, {"color": "#F8AD3C", "id": "C2396 - Industri Barang dari Batu ", "label": "Industri Barang dari Batu \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Batu \n\nSub-Industri:\nC23961 - Industri Barang dari Marmer \u0026 Granit untuk Rumah Tangga\nC23962 - Industri Barang dari Marmer \u0026 Granit untuk Bahan Bangunan\nC23963 - Industri Barang dari Batu untuk Rumah Tangga \u0026 Bangunan\nC23969 - Industri Barang dari Marmer, Granit \u0026 Batu Lainnya "}, {"color": "#F8AD3C", "id": "C2399 - Industri Barang Galian Bukan Logam Lainnya Ytdl ", "label": "Industri Barang Galian Bukan Logam\nLainnya Ytdl \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang Galian Bukan Logam Lainnya Ytdl "}, {"color": "#F8AD3C", "id": "C24 - Industri Logam Dasar", "label": "Industri Logam Dasar\nRp365.01 Tn", "physics": false, "shape": "dot", "size": 15.336834810733045, "title": "Industri Logam Dasar", "x": -562.9165259377173, "y": 324.99997581248266}, {"color": "#F8AD3C", "id": "C241 - Industri Logam Dasar Besi \u0026 Baja", "label": "Industri Logam Dasar Besi \u0026 Baja\nRp165.9 Tn", "shape": "dot", "size": 12.42560944874608, "title": "Industri Logam Dasar Besi dan Baja"}, {"color": "#F8AD3C", "id": "C2410 - Industri Logam Dasar Besi \u0026 Baja", "label": "Industri Logam Dasar Besi \u0026 Baja\nRp165.9 Tn", "shape": "dot", "size": 12.42560944874608, "title": "Industri Logam Dasar Besi dan Baja\n\nSub-Industri:\nC24101 - Industri Besi \u0026 Baja Dasar\nC24102 - Industri Penggilingan Baja\nC24103 - Industri Pipa \u0026 Sambungan Pipa dari Baja \u0026 Besi"}, {"color": "#F8AD3C", "id": "C242 - Industri Logam Dasar Mulia \u0026 Logam Dasar Bukan Besi Lainnya ", "label": "Industri Logam Dasar Mulia\n\u0026 Logam Dasar Bukan Besi Lainnya \nRp171.34 Tn", "shape": "dot", "size": 12.505195807751832, "title": "Industri Logam Dasar Mulia dan Logam Dasar Bukan Besi Lainnya "}, {"color": "#F8AD3C", "id": "C2420 - Industri Logam Dasar Mulia \u0026 Logam Dasar Bukan Besi Lainnya ", "label": "Industri Logam Dasar Mulia\n\u0026 Logam Dasar Bukan Besi Lainnya \nRp171.34 Tn", "shape": "dot", "size": 12.505195807751832, "title": "Industri Logam Dasar Mulia dan Logam Dasar Bukan Besi Lainnya \n\nSub-Industri:\nC24201 - Industri Pembuatan Logam Dasar Mulia\nC24202 - Industri Pembuatan Logam Dasar Bukan Besi\nC24203 - Industri Penggilingan Logam Bukan Besi\nC24204 - Industri Ekstrusi Logam Bukan Besi\nC24205 - Industri Pipa \u0026 Sambungan Pipa dari Logam Bukan Besi \u0026 Baja \nC24206 - Industri Pengolahan Uranium \u0026 Bijih Uranium"}, {"color": "#F8AD3C", "id": "C243 - Industri Pengecoran Logam", "label": "Industri Pengecoran Logam\nRp27.77 Tn", "shape": "dot", "size": 10.406029554235134, "title": "Industri Pengecoran Logam"}, {"color": "#F8AD3C", "id": "C2431 - Industri Pengecoran Besi \u0026 Baja", "label": "Industri Pengecoran Besi \u0026 Baja\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengecoran Besi dan Baja"}, {"color": "#F8AD3C", "id": "C2432 - Industri Pengecoran Logam Bukan Besi \u0026 Baja ", "label": "Industri Pengecoran Logam\nBukan Besi \u0026 Baja \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pengecoran Logam Bukan Besi dan Baja "}, {"color": "#F8AD3C", "id": "C25 - Industri Barang Logam, Bukan Mesin \u0026 Peralatannya", "label": "Industri Barang Logam,\nBukan Mesin \u0026 Peralatannya\nRp247.07 Tn", "physics": false, "shape": "dot", "size": 13.612476199906139, "title": "Industri Barang Logam, Bukan Mesin dan Peralatannya", "x": -459.6194247776385, "y": 459.6193546633941}, {"color": "#F8AD3C", "id": "C251 - Industri Barang Logam Siap Pasang", "label": "Industri Barang Logam\nSiap Pasang\nRp79.87 Tn", "shape": "dot", "size": 11.167715946281543, "title": "Industri Barang Logam Siap Pasang untuk Bangunan, Tangki, Tandon Air dan Generator Uap "}, {"color": "#F8AD3C", "id": "C2511 - Industri Barang Logam Siap Pasang untuk Bangunan ", "label": "Industri Barang Logam\nSiap Pasang untuk Bangunan \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang Logam Siap Pasang untuk Bangunan \n\nSub-Industri:\nC25111 - Industri Barang dari Logam Bukan Aluminium Siap Pasang untuk Bangunan \nC25112 - Industri Barang dari Logam Aluminium Siap Pasang untuk Bangunan \nC25113 - Industri Konstruksi Berat Siap Pasang dari Baja untuk Bangunan \nC25119 - Industri Barang dari Logam Siap Pasang untuk Konstruksi Lainnya "}, {"color": "#F8AD3C", "id": "C2512 - Industri Tangki, Tandon Air \u0026 Wadah dari Logam ", "label": "Industri Tangki, Tandon Air\n\u0026 Wadah dari Logam \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Tangki, Tandon Air dan Wadah dari Logam "}, {"color": "#F8AD3C", "id": "C2513 - Industri Generator Uap, Bukan Ketel Pemanas ", "label": "Industri Generator Uap,\nBukan Ketel Pemanas \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Generator Uap, Bukan Ketel Pemanas "}, {"color": "#F8AD3C", "id": "C252 - Industri Senjata \u0026 Amunisi", "label": "Industri Senjata \u0026 Amunisi\nRp15.28 Tn", "shape": "dot", "size": 10.223337521820847, "title": "Industri Senjata dan Amunisi"}, {"color": "#F8AD3C", "id": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "label": "Industri Barang Logam Lainnya\n\u0026 Jasa Pembuatan Barang Logam \nRp151.93 Tn", "shape": "dot", "size": 12.221422731803747, "title": "Industri Barang Logam Lainnya dan Jasa Pembuatan Barang Logam "}, {"color": "#F8AD3C", "id": "C2591 - Industri Penempaan, Pengepresan, Pencetakan \u0026 Pembentukan Logam", "label": "Industri Penempaan, Pengepresan,\nPencetakan \u0026 Pembentukan Logam\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Penempaan, Pengepresan, Pencetakan dan Pembentukan Logam; Metalurgi Bubuk "}, {"color": "#F8AD3C", "id": "C2592 - Jasa Industri untuk Berbagai Pengerjaan Khusus Barang Logam", "label": "Jasa Industri untuk Berbagai Pengerjaan\nKhusus Barang Logam\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Jasa Industri untuk Berbagai Pengerjaan Khusus Logam dan Barang dari Logam "}, {"color": "#F8AD3C", "id": "C2593 - Industri Alat Potong, Perkakas Tangan \u0026 Peralatan Umum ", "label": "Industri Alat Potong, Perkakas Tangan\n\u0026 Peralatan Umum \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Alat Potong, Perkakas Tangan dan Peralatan Umum \n\nSub-Industri:\nC25931 - Industri Alat Potong \u0026 Perkakas Tangan untuk Pertanian \nC25932 - Industri Alat Potong \u0026 Perkakas Tangan Pertukangan \nC25933 - Industri Alat Potong \u0026 Perkakas Tangan Rumah Tangga\nC25934 - Industri Peralatan Umum "}, {"color": "#F8AD3C", "id": "C2594 - Industri Ember, Kaleng, Drum \u0026 Wadah Sejenis dari Logam ", "label": "Industri Ember, Kaleng, Drum\n\u0026 Wadah Sejenis dari Logam \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Ember, Kaleng, Drum dan Wadah Sejenis dari Logam "}, {"color": "#F8AD3C", "id": "C2595 - Industri Barang dari Kawat \u0026 Paku, Mur \u0026 Baut", "label": "Industri Barang dari Kawat\n\u0026 Paku, Mur \u0026 Baut\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Barang dari Kawat dan Paku, Mur dan Baut, Bukan Kabel Logam \n\nSub-Industri:\nC25951 - Industri Barang dari Kawat\nC25952 - Industri Paku, Mur \u0026 Baut"}, {"color": "#F8AD3C", "id": "C2599 - Industri Barang Logam Lainnya", "label": "Industri Barang Logam Lainnya\nRp106.42 Tn", "shape": "dot", "size": 11.556015887147645, "title": "Industri Barang Logam Lainnya Ytdl\n\nSub-Industri:\nC25991 - Industri Brankas, Filling Kantor \u0026 Sejenisnya \nC25992 - Industri Peralatan Dapur \u0026 Peralatan Meja dari Logam \nC25993 - Industri Keperluan Rumah Tangga dari Logam Lainnya\nC25994 - Industri Pembuatan Baja Profil \nC25995 - Industri Lampu dari Logam \nC25999 - Industri Barang Logam Lainnya"}, {"color": "#F8AD3C", "id": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "label": "Industri Komputer, Barang Elektronik\n\u0026 Optik\nRp416.6 Tn", "physics": false, "shape": "dot", "size": 16.091170577234266, "title": "Industri Komputer, Barang Elektronik dan Optik", "x": -324.99992969767123, "y": 562.9165333095101}, {"color": "#F8AD3C", "id": "C261 - Industri Komponen \u0026 Papan Elektronik", "label": "Industri Komponen \u0026 Papan\nElektronik\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Komponen dan Papan Elektronik"}, {"color": "#F8AD3C", "id": "C2611 - Industri Tabung Elektron \u0026 Konektor Elektronik ", "label": "Industri Tabung Elektron \u0026 Konektor\nElektronik \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Tabung Elektron dan Konektor Elektronik "}, {"color": "#F8AD3C", "id": "C2612 - Industri Semi Konduktor \u0026 Komponen Elektronik Lainnya ", "label": "Industri Semi Konduktor \u0026 Komponen\nElektronik Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Semi Konduktor dan Komponen Elektronik Lainnya "}, {"color": "#F8AD3C", "id": "C262 - Industri Komputer \u0026 Perlengkapannya", "label": "Industri Komputer\n\u0026 Perlengkapannya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Komputer dan Perlengkapannya"}, {"color": "#F8AD3C", "id": "C2621 - Industri Komputer \u0026 Perakitan Komputer", "label": "Industri Komputer\n\u0026 Perakitan Komputer\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Komputer dan/atau Perakitan Komputer"}, {"color": "#F8AD3C", "id": "C2622 - Industri Perlengkapan Komputer", "label": "Industri Perlengkapan Komputer\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Perlengkapan Komputer"}, {"color": "#F8AD3C", "id": "C263 - Industri Peralatan Komunikasi", "label": "Industri Peralatan Komunikasi\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Komunikasi"}, {"color": "#F8AD3C", "id": "C2631 - Industri Peralatan Telepon \u0026 Faksimili ", "label": "Industri Peralatan Telepon\n\u0026 Faksimili \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Telepon dan Faksimili "}, {"color": "#F8AD3C", "id": "C2632 - Industri Peralatan Komunikasi Tanpa Kabel", "label": "Industri Peralatan Komunikasi\nTanpa Kabel\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Komunikasi Tanpa Kabel (Wireless) "}, {"color": "#F8AD3C", "id": "C2639 - Industri Peralatan Komunikasi Lainnya", "label": "Industri Peralatan Komunikasi\nLainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Komunikasi Lainnya\n\nSub-Industri:\nC26391 - Industri Kartu Cerdas\nC26399 - Industri Peralatan Komunikasi Lainnya"}, {"color": "#F8AD3C", "id": "C264 - Industri Peralatan Audio \u0026 Video Elektronik", "label": "Industri Peralatan Audio\n\u0026 Video Elektronik\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Audio dan Video Elektronik"}, {"color": "#F8AD3C", "id": "C2641 - Industri Televisi \u0026 Perakitan Televisi", "label": "Industri Televisi\n\u0026 Perakitan Televisi\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Televisi dan/atau Perakitan Televisi "}, {"color": "#F8AD3C", "id": "C2642 - Industri Peralatan Perekam Audio \u0026 Video", "label": "Industri Peralatan Perekam\nAudio \u0026 Video\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Perekam, Penerima dan Pengganda Audio dan Video, Bukan Industri Televisi "}, {"color": "#F8AD3C", "id": "C2649 - Industri Peralatan Audio \u0026 Video Elektronik Lainnya", "label": "Industri Peralatan Audio \u0026 Video\nElektronik Lainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Audio dan Video Elektronik Lainnya"}, {"color": "#F8AD3C", "id": "C265 - Industri Alat Ukur, Alat Uji, Alat Navigasi \u0026 Alat Ukur Waktu ", "label": "Industri Alat Ukur, Alat Uji,\nAlat Navigasi \u0026 Alat Ukur Waktu \nRp71.69 Tn", "shape": "dot", "size": 11.048193509322083, "title": "Industri Alat Ukur, Alat Uji, Peralatan Navigasi dan Kontrol dan Alat Ukur Waktu "}, {"color": "#F8AD3C", "id": "C2651 - Industri Alat Ukur, Alat Uji, Alat Navigasi \u0026 Kontrol ", "label": "Industri Alat Ukur, Alat Uji,\nAlat Navigasi \u0026 Kontrol \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Alat Ukur, Alat Uji, Peralatan Navigasi dan Kontrol \nC26511 - Industri Alat Ukur \u0026 Alat Uji Manual \nC26512 - Industri Alat Ukur \u0026 Alat Uji Elektrik \nC26513 - Industri Alat Ukur \u0026 Alat Uji Elektronik \nC26514 - Industri Alat Uji dalam Proses Industri"}, {"color": "#F8AD3C", "id": "C2652 - Industri Alat Ukur Waktu", "label": "Industri Alat Ukur Waktu\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Alat Ukur Waktu"}, {"color": "#F8AD3C", "id": "C266 - Industri Peralatan Iradiasi, Elektromedikal \u0026 Elektroterapi ", "label": "Industri Peralatan Iradiasi,\nElektromedikal \u0026 Elektroterapi \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Iradiasi, Elektromedikal dan Elektroterapi \n\nSub-Industri:\nC26601 - Industri Peralatan Iradiasi/Sinar X, Perlengkapan \u0026 Sejenisnya \nC26602 - Industri Peralatan Elektromedikal \u0026 Elektroterapi"}, {"color": "#F8AD3C", "id": "C267 - Industri Peralatan Fotografi \u0026 Instrumen Optik Bukan Kaca Mata ", "label": "Industri Peralatan Fotografi\n\u0026 Instrumen Optik\nBukan Kaca Mata \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Fotografi dan Instrumen Optik Bukan Kaca Mata "}, {"color": "#F8AD3C", "id": "C2671 - Industri Peralatan Fotografi", "label": "Industri Peralatan Fotografi\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Fotografi"}, {"color": "#F8AD3C", "id": "C2679 - Industri Peralatan Fotografi \u0026 Instrumen Optik Lainnya", "label": "Industri Peralatan Fotografi\n\u0026 Instrumen Optik Lainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Fotografi dan Instrumen Optik Lainnya\n\nSub-Industri:\nC26791 - Industri Kamera Cinematografi Proyektor \u0026 Perlengkapannya \nC26792 - Industri Teropong \u0026 Instrumen Optik Bukan Kaca Mata"}, {"color": "#F8AD3C", "id": "C268 - Industri Media Magnetik \u0026 Media Optik", "label": "Industri Media Magnetik\n\u0026 Media Optik\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Media Magnetik dan Media Optik"}, {"color": "#F8AD3C", "id": "C27 - Industri Peralatan Listrik", "label": "Industri Peralatan Listrik\nRp319.32 Tn", "physics": false, "shape": "dot", "size": 14.668833336089069, "title": "Industri Peralatan Listrik", "x": -168.23232939243348, "y": 627.8517696670654}, {"color": "#F8AD3C", "id": "C271 - Industri Motor Listrik, Generator, Transformator \u0026 Pendistribusian Listrik", "label": "Industri Motor Listrik,\nGenerator,Transformator\n\u0026 Pendistribusian Listrik\nRp122.05 Tn", "shape": "dot", "size": 11.78451424569889, "title": "Industri Motor Listrik, Generator, Transformator dan Peralatan Pengontrol dan Pendistribusian Listrik"}, {"color": "#F8AD3C", "id": "C2711 - Industri Motor Listrik, Generator \u0026 Transformator ", "label": "Industri Motor Listrik,\nGenerator \u0026 Transformator \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Motor Listrik, Generator dan Transformator \n\nSub-Industri:\nC27111 - Industri Motor Listrik\nC27112 - Industri Mesin Pembangkit Listrik\nC27113 - Industri Transformator, Rectifier \u0026 Stabilizer"}, {"color": "#F8AD3C", "id": "C2712 - Industri Peralatan Pengontrol \u0026 Pendistribusian Listrik ", "label": "Industri Peralatan Pengontrol\n\u0026 Pendistribusian Listrik \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Pengontrol dan Pendistribusian Listrik "}, {"color": "#F8AD3C", "id": "C272 - Industri Batu Baterai \u0026 Akumulator Listrik", "label": "Industri Batu Baterai\n\u0026 Akumulator Listrik\nRp54.36 Tn", "shape": "dot", "size": 10.794847883045605, "title": "Industri Batu Baterai dan Akumulator Listrik\n\nSub-Industri:\nC27201 - Industri Batu Baterai\nC27202 - Industri Akumulator Listrik\nC27203 - Industri Baterai untuk Kendaraan Bermotor Listrik"}, {"color": "#F8AD3C", "id": "C273 - Industri Kabel \u0026 Perlengkapannya", "label": "Industri Kabel \u0026 Perlengkapannya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Kabel dan Perlengkapannya"}, {"color": "#F8AD3C", "id": "C2731 - Industri Kabel Serat Optik", "label": "Industri Kabel Serat Optik\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Kabel Serat Optik"}, {"color": "#F8AD3C", "id": "C2732 - Industri Kabel Listrik \u0026 Elektronik Lainnya ", "label": "Industri Kabel Listrik\n\u0026 Elektronik Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Kabel Listrik dan Elektronik Lainnya "}, {"color": "#F8AD3C", "id": "C2733 - Industri Perlengkapan Kabel", "label": "Industri Perlengkapan Kabel\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Perlengkapan Kabel"}, {"color": "#F8AD3C", "id": "C274 - Industri Peralatan Penerangan Listrik ", "label": "Industri Peralatan Penerangan Listrik \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Penerangan Listrik (Termasuk Peralatan Penerangan Bukan Listrik) \n\nSub-Industri:\nC27401 - Industri Bola Lampu Pijar, Penerangan Terpusat \u0026 Ultra Violet \nC27402 - Industri Lampu Tabung Gas\nC27403 - Industri Peralatan Penerangan Alat Transportasi\nC27404 - Industri Lampu Led\nC27409 - Industri Peralatan Penerangan Lainnya"}, {"color": "#F8AD3C", "id": "C275 - Industri Peralatan Rumah Tangga", "label": "Industri Peralatan\nRumah Tangga\nRp57.46 Tn", "shape": "dot", "size": 10.840133788917667, "title": "Industri Peralatan Rumah Tangga"}, {"color": "#F8AD3C", "id": "C2751 - Industri Peralatan Listrik Rumah Tangga ", "label": "Industri Peralatan Listrik\nRumah Tangga \nRp57.46 Tn", "shape": "dot", "size": 10.840133788917667, "title": "Industri Peralatan Listrik Rumah Tangga "}, {"color": "#F8AD3C", "id": "C2752 - Industri Peralatan Elektrotermal Rumah Tangga ", "label": "Industri Peralatan Elektrotermal\nRumah Tangga \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Elektrotermal Rumah Tangga "}, {"color": "#F8AD3C", "id": "C2753 - Industri Peralatan Pemanas \u0026 Masak Bukan Listrik Rumah Tangga ", "label": "Industri Peralatan Pemanas\n\u0026 Masak Bukan Listrik\nRumah Tangga \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Pemanas dan Masak Bukan Listrik Rumah Tangga "}, {"color": "#F8AD3C", "id": "C279 - Industri Peralatan Listrik Lainnya", "label": "Industri Peralatan Listrik\nLainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Listrik Lainnya"}, {"color": "#F8AD3C", "id": "C28 - Industri Mesin \u0026 Perlengkapan Lainnya", "label": "Industri Mesin \u0026 Perlengkapan\nLainnya\nRp527.1 Tn", "physics": false, "shape": "dot", "size": 17.706713147224477, "title": "Industri Mesin dan Perlengkapan Ytdl", "x": 1.587637288070785e-05, "y": 649.9999911213918}, {"color": "#F8AD3C", "id": "C281 - Industri Mesin untuk Keperluan Umum", "label": "Industri Mesin\nuntuk Keperluan Umum\nRp527.1 Tn", "shape": "dot", "size": 17.706713147224477, "title": "Industri Mesin untuk Keperluan Umum"}, {"color": "#F8AD3C", "id": "C2811 - Industri Mesin \u0026 Turbin, Bukan Mesin Pesawat \u0026 Kendaraan Bermotor", "label": "Industri Mesin \u0026 Turbin,\nBukan Mesin Pesawat\n\u0026 Kendaraan Bermotor\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin dan Turbin, Bukan Mesin Pesawat Terbang dan Kendaraan Bermotor \n\nSub-Industri:\nC28111 - Industri Mesin Uap, Turbin \u0026 Kincir \nC28112 - Industri Motor Pembakaran dalam\nC28113 - Industri Komponen \u0026 Suku Cadang Mesin \u0026 Turbin "}, {"color": "#F8AD3C", "id": "C2812 - Industri Peralatan Tenaga Zat Cair \u0026 Gas ", "label": "Industri Peralatan\nTenaga Zat Cair \u0026 Gas \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Peralatan Tenaga Zat Cair dan Gas "}, {"color": "#F8AD3C", "id": "C2813 - Industri Pompa Lainnya, Kompresor, Kran \u0026 Klep/Katup ", "label": "Industri Pompa Lainnya,\nKompresor, Kran \u0026 Klep/Katup \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pompa Lainnya, Kompresor, Kran dan Klep/Katup "}, {"color": "#F8AD3C", "id": "C2814 - Industri Bearing, Roda Gigi \u0026 Elemen Penggerak Mesin ", "label": "Industri Bearing, Roda Gigi\n\u0026 Elemen Penggerak Mesin \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Bearing, Roda Gigi dan Elemen Penggerak Mesin "}, {"color": "#F8AD3C", "id": "C2815 - Industri Oven, Perapian \u0026 Tungku Pembakar", "label": "Industri Oven, Perapian\n\u0026 Tungku Pembakar\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Oven, Perapian dan Tungku Pembakar\n\nSub-Industri:\nC28151 - Industri Oven, Perapian \u0026 Tungku Pembakar Tidak Menggunakan Listrik\nC28152 - Industri Oven, Perapian \u0026 Tungku Pembakar Menggunakan Listrik"}, {"color": "#F8AD3C", "id": "C2816 - Industri Alat Pengangkat \u0026 Pemindah", "label": "Industri Alat Pengangkat\n\u0026 Pemindah\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Alat Pengangkat dan Pemindah"}, {"color": "#F8AD3C", "id": "C2817 - Industri Mesin \u0026 Peralatan Kantor (Bukan Komputer)", "label": "Industri Mesin \u0026 Peralatan Kantor\n(Bukan Komputer)\nRp31.65 Tn", "shape": "dot", "size": 10.46281234118871, "title": "Industri Mesin dan Peralatan Kantor (Bukan Komputer dan Peralatan Perlengkapannya)\n\nSub-Industri:\nC28171 - Industri Mesin Kantor \u0026 Akuntansi Manual \nC28172 - Industri Mesin Kantor \u0026 Akuntansi Elektrik \nC28173 - Industri Mesin Kantor \u0026 Akuntansi Elektronik \nC28174 - Industri Mesin Fotocopi \nC28179 - Industri Mesin \u0026 Peralatan Kantor Lainnya "}, {"color": "#F8AD3C", "id": "C2818 - Industri Perkakas Tangan yang Digerakkan Tenaga ", "label": "Industri Perkakas Tangan\nyang Digerakkan Tenaga \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Perkakas Tangan yang Digerakkan Tenaga "}, {"color": "#F8AD3C", "id": "C2819 - Industri Mesin untuk Keperluan Umum Lainnya ", "label": "Industri Mesin\nuntuk Keperluan Umum Lainnya \nRp305.46 Tn", "shape": "dot", "size": 14.466063585184045, "title": "Industri Mesin untuk Keperluan Umum Lainnya \n\nSub-Industri:\nC28191 - Industri Mesin untuk Pembungkus, Pembotolan \u0026 Pengalengan \nC28192 - Industri Mesin Timbangan \nC28193 - Industri Mesin Pendingin \nC28199 - Industri Mesin untuk Keperluan Umum Lainnya "}, {"color": "#F8AD3C", "id": "C282 - Industri Mesin untuk Keperluan Khusus", "label": "Industri Mesin\nuntuk Keperluan Khusus\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin untuk Keperluan Khusus"}, {"color": "#F8AD3C", "id": "C2821 - Industri Mesin Pertanian \u0026 Kehutanan", "label": "Industri Mesin\nPertanian \u0026 Kehutanan\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin Pertanian dan Kehutanan"}, {"color": "#F8AD3C", "id": "C2822 - Industri Mesin \u0026 Perkakas Mesin", "label": "Industri Mesin \u0026 Perkakas Mesin\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin dan Perkakas Mesin untuk Pengerjaan Logam, Kayu dan Bahan Lainnya\n\nSub-Industri:\nC28221 - Industri Mesin \u0026 Perkakas Mesin untuk Pengerjaan Logam \nC28222 - Industri Mesin \u0026 Perkakas Mesin untuk Pengerjaan Kayu \nC28223 - Industri Mesin \u0026 Perkakas Mesin untuk Pengerjaan Bahan Lainnya\nC28224 - Industri Mesin \u0026 Perkakas Mesin untuk Pengelasan dengan Listrik"}, {"color": "#F8AD3C", "id": "C2823 - Industri Mesin Metalurgi ", "label": "Industri Mesin Metalurgi \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin Metalurgi "}, {"color": "#F8AD3C", "id": "C2824 - Industri Mesin Penambangan, Penggalian \u0026 Konstruksi ", "label": "Industri Mesin Penambangan,\nPenggalian \u0026 Konstruksi \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin Penambangan, Penggalian dan Konstruksi "}, {"color": "#F8AD3C", "id": "C2825 - Industri Mesin Pengolahan Makanan, Minuman \u0026 Tembakau ", "label": "Industri Mesin Pengolahan\nMakanan, Minuman \u0026 Tembakau \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin Pengolahan Makanan, Minuman dan Tembakau "}, {"color": "#F8AD3C", "id": "C2826 - Industri Mesin Tekstil, Pakaian Jadi \u0026 Produk Kulit ", "label": "Industri Mesin Tekstil,\nPakaian Jadi \u0026 Produk Kulit \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin Tekstil, Pakaian Jadi dan Produk Kulit \n\nSub-Industri:\nC28261 - Industri Kabinet Mesin Jahit \nC28262 - Industri Mesin Jahit, Mesin Cuci \u0026 Mesin Pengering untuk Niaga\nC28263 - Industri Mesin Tekstil \nC28264 - Industri Jarum Mesin Jahit, Rajut, Bordir \u0026 Sejenisnya \nC28265 - Industri Mesin Penyiapan \u0026 Pembuatan Produk Kulit "}, {"color": "#F8AD3C", "id": "C2829 - Industri Mesin Keperluan Khusus Lainnya ", "label": "Industri Mesin\nKeperluan Khusus Lainnya\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Mesin Keperluan Khusus Lainnya \n\nSub-Industri:\nC28291 - Industri Mesin Percetakan \nC28292 - Industri Mesin Pabrik Kertas \nC28299 - Industri Mesin Keperluan Khusus Lainnya"}, {"color": "#F8AD3C", "id": "C29 - Industri Kendaraan Bermotor, Trailer \u0026 Semi Trailer", "label": "Industri Kendaraan Bermotor,\nTrailer \u0026 Semi Trailer\nRp414.71 Tn", "physics": false, "shape": "dot", "size": 16.06341886029845, "title": "Industri Kendaraan Bermotor, Trailer dan Semi Trailer", "x": 168.23234564283462, "y": 627.8517696670654}, {"color": "#F8AD3C", "id": "C291 - Industri Kendaraan Bermotor Roda Empat atau Lebih", "label": "Industri Kendaraan Bermotor\nRoda Empat atau Lebih\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Kendaraan Bermotor Roda Empat atau Lebih\n\nSub-Industri:\nC29101 - Industri Kendaraan Bermotor Roda Empat atau Lebih\nC29102 - Industri Kendaraan Multiguna Pedesaan"}, {"color": "#F8AD3C", "id": "C292 - Industri Karoseri Kendaraan Bermotor Roda Empat atau Lebih", "label": "Industri Karoseri Kendaraan Bermotor\nRoda Empat atau Lebih\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Karoseri Kendaraan Bermotor Roda Empat atau Lebih dan Industri Trailer dan Semi Trailer "}, {"color": "#F8AD3C", "id": "C293 - Industri Suku Cadang \u0026 Aksesori Kendaraan Bermotor Roda Empat atau Lebih ", "label": "Industri Suku Cadang\n\u0026 Aksesori Kendaraan Bermotor\nRoda Empat atau Lebih \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Suku Cadang dan Aksesori Kendaraan Bermotor Roda Empat atau Lebih "}, {"color": "#F8AD3C", "id": "C30 - Industri Alat Angkutan Lainnya", "label": "Industri Alat Angkutan Lainnya\nRp264.44 Tn", "physics": false, "shape": "dot", "size": 13.86634145691292, "title": "Industri Alat Angkutan Lainnya", "x": 324.99994594807237, "y": 562.9165333095101}, {"color": "#F8AD3C", "id": "C301 - Industri Pembuatan Kapal \u0026 Perahu", "label": "Industri Pembuatan Kapal \u0026 Perahu\nRp41.84 Tn", "shape": "dot", "size": 10.611764840934624, "title": "Industri Pembuatan Kapal dan Perahu"}, {"color": "#F8AD3C", "id": "C3011 - Industri Pembuatan Kapal, Perahu \u0026 Bangunan Terapung ", "label": "Industri Pembuatan Kapal,\nPerahu \u0026 Bangunan Terapung \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pembuatan Kapal, Perahu dan Struktur Bangunan Terapung \n\nSub-Industri:\nC30111 - Industri Kapal \u0026 Perahu\nC30112 - Industri Bangunan Lepas Pantai \u0026 Bangunan Terapung\nC30113 - Industri Peralatan, Perlengkapan \u0026 Bagian Kapal"}, {"color": "#F8AD3C", "id": "C3012 - Industri Pembuatan Kapal \u0026 Perahu untuk Wisata, Rekreasi \u0026 Olahraga ", "label": "Industri Pembuatan Kapal \u0026 Perahu\nuntuk Wisata, Rekreasi \u0026 Olahraga \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Pembuatan Kapal dan Perahu untuk Tujuan Wisata atau Rekreasi dan Olahraga "}, {"color": "#F8AD3C", "id": "C302 - Industri Lokomotif \u0026 Gerbong Kereta", "label": "Industri Lokomotif\n\u0026 Gerbong Kereta\nRp6.14 Tn", "shape": "dot", "size": 10.089793637676623, "title": "Industri Lokomotif dan Gerbong Kereta"}, {"color": "#F8AD3C", "id": "C303 - Industri Pesawat Terbang \u0026 Perlengkapannya", "label": "Industri Pesawat Terbang\n\u0026 Perlengkapannya\nRp58.97 Tn", "shape": "dot", "size": 10.862150297327664, "title": "Industri Pesawat Terbang dan Perlengkapannya"}, {"color": "#F8AD3C", "id": "C304 - Industri Kendaraan Perang", "label": "Industri Kendaraan Perang\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Kendaraan Perang"}, {"color": "#F8AD3C", "id": "C309 - Industri Alat Angkutan Lainnya Ytdl", "label": "Industri Alat Angkutan\nLainnya Ytdl\nRp157.49 Tn", "shape": "dot", "size": 12.302632680974007, "title": "Industri Alat Angkutan Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C3091 - Industri Sepeda Motor ", "label": "Industri Sepeda Motor \nRp128.41 Tn", "shape": "dot", "size": 11.877515221449602, "title": "Industri Sepeda Motor \n\nSub-Industri:\nC30911 - Industri Sepeda Motor Roda Dua \u0026 Tiga\nC30912 - Industri Komponen Sepeda Motor Roda Dua \u0026 Tiga "}, {"color": "#F8AD3C", "id": "C3092 - Industri Sepeda \u0026 Kursi Roda ", "label": "Industri Sepeda \u0026 Kursi Roda \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Sepeda dan Kursi Roda \n\nSub-Industri:\nC30921 - Industri Sepeda \u0026 Kursi Roda Termasuk Becak\nC30922 - Industri Perlengkapan Sepeda, Kursi Roda \u0026 Becak "}, {"color": "#F8AD3C", "id": "C3099 - Industri Alat Angkutan Lainnya Ytdl", "label": "Industri Alat Angkutan\nLainnya Ytdl (Sub)\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Alat Angkutan Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C31 - Industri Furnitur", "label": "Industri Furnitur\nRp84.75 Tn", "physics": false, "shape": "dot", "size": 11.239147860421346, "title": "Industri Furnitur\n\nSub-Industri:\nC31001 - Industri Furnitur dari Kayu\nC31002 - Industri Furnitur dari Rotan \u0026 Bambu\nC31003 - Industri Furnitur dari Plastik\nC31004 - Industri Furnitur dari Logam\nC31009 - Industri Furnitur Lainnya", "x": 459.61932479898377, "y": 459.61947089244995}, {"color": "#F8AD3C", "id": "C32 - Industri Pengolahan Lainnya", "label": "Industri Pengolahan Lainnya\nRp173.71 Tn", "physics": false, "shape": "dot", "size": 12.539853072037529, "title": "Industri Pengolahan Lainnya", "x": 562.9164647020812, "y": 325.0001114130479}, {"color": "#F8AD3C", "id": "C321 - Industri Barang Perhiasan \u0026 Barang Berharga", "label": "Industri Barang Perhiasan\n\u0026 Barang Berharga\nRp74.73 Tn", "shape": "dot", "size": 11.09265190983559, "title": "Industri Barang Perhiasan dan Barang Berharga"}, {"color": "#F8AD3C", "id": "C3211 - Industri Perhiasan \u0026 Barang Sejenis ", "label": "Industri Perhiasan\n\u0026 Barang Sejenis \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Perhiasan dan Barang Sejenis \n\nSub-Industri:\nC32111 - Industri Permata \nC32112 - Industri Barang Perhiasan dari Logam Mulia untuk Pribadi\nC32113 - Industri Barang Perhiasan dari Logam Mulia Bukan untuk Pribadi\nC32114 - Industri Barang dari Logam Mulia untuk Teknik \u0026 Laboratorium \nC32115 - Industri Perhiasan Mutiara\nC32119 - Industri Barang Lainnya dari Logam Mulia"}, {"color": "#F8AD3C", "id": "C3212 - Industri Perhiasan Imitasi \u0026 Barang Sejenis ", "label": "Industri Perhiasan Imitasi\n\u0026 Barang Sejenis \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Industri Perhiasan Imitasi dan Barang Sejenis "}, {"color": "#F8AD3C", "id": "C322 - Industri Alat Musik", "label": "Industri Alat Musik\nRp13.26 Tn", "shape": "dot", "size": 10.193901025789563, "title": "Industri Alat Musik\n\nSub-Industri:\nC32201 - Industri Alat Musik Tradisional\nC32202 - Industri Alat Musik Bukan Tradisional"}, {"color": "#F8AD3C", "id": "C323 - Industri Alat Olahraga", "label": "Industri Alat Olahraga\nRp6.68 Tn", "shape": "dot", "size": 10.097674333353956, "title": "Industri Alat Olahraga"}, {"color": "#F8AD3C", "id": "C324 - Industri Alat Permainan \u0026 Mainan Anak-Anak", "label": "Industri Alat Permainan\n\u0026 Mainan Anak-Anak\nRp14.2 Tn", "shape": "dot", "size": 10.207631982728133, "title": "Industri Alat Permainan dan Mainan Anak-Anak\n\nSub-Industri:\nC32401 - Industri Alat Permainan\nC32402 - Industri Mainan Anak-Anak"}, {"color": "#F8AD3C", "id": "C325 - Industri Peralatan Kedokteran serta Perlengkapannya", "label": "Industri Peralatan Kedokteran\nserta Perlengkapannya\nRp24.95 Tn", "shape": "dot", "size": 10.364741442153887, "title": "Industri Peralatan Kedokteran dan Kedokteran Gigi serta Perlengkapannya\n\nSub-Industri:\nC32501 - Industri Furnitur untuk Operasi, Perawatan Kedokteran \u0026 Gigi \nC32502 - Industri Peralatan Kedokteran \u0026 Gigi, Orthopaedic \u0026 Prosthetic \nC32503 - Industri Kaca Mata\nC32509 - Industri Peralatan Kedokteran serta Perlengkapan Lainnya "}, {"color": "#F8AD3C", "id": "C329 - Industri Pengolahan Lainnya", "label": "Industri Pengolahan Lainnya (Sub)\nRp39.89 Tn", "shape": "dot", "size": 10.583252378176399, "title": "Industri Pengolahan Lainnya Ytdl\n\nSub-Industri:\nC32901 - Industri Alat Tulis \u0026 Gambar Termasuk Perlengkapannya\nC32902 - Industri Pita Mesin Tulis/Gambar\nC32903 - Industri Kerajinan\nC32904 - Industri Peralatan untuk Pelindung Keselamatan\nC32905 - Industri Serat Sabut Kelapa\nC32906 - Industri Produksi Radioisotop\nC32907 - Industri Fabrikasi Elemen Bakar Uranium\nC32909 - Industri Pengolahan Lainnya Ytdl"}, {"color": "#F8AD3C", "id": "C33 - Reparasi \u0026 Pemasangan Mesin \u0026 Peralatan", "label": "Reparasi \u0026 Pemasangan\nMesin \u0026 Peralatan\nRp21.18 Tn", "physics": false, "shape": "dot", "size": 10.309678027607761, "title": "Reparasi dan Pemasangan Mesin dan Peralatan", "x": 627.8518172886922, "y": 168.23223990667978}, {"color": "#F8AD3C", "id": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "label": "Reparasi Produk Logam\nPabrikasi, Mesin \u0026 Peralatan \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reparasi Produk Logam Pabrikasi, Mesin dan Peralatan "}, {"color": "#F8AD3C", "id": "C3311 - Reparasi Produk Logam Pabrikasi ", "label": "Reparasi Produk Logam Pabrikasi \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reparasi Produk Logam Pabrikasi \n\nSub-Industri:\nC33111 - Reparasi Produk Logam Siap Pasang untuk Bangunan, Tangki \u0026 Generator Uap\nC33112 - Reparasi Produk Senjata \u0026 Amunisi \nC33119 - Reparasi Produk Logam Pabrikasi Lainnya "}, {"color": "#F8AD3C", "id": "C3312 - Reparasi Mesin", "label": "Reparasi Mesin\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reparasi Mesin\n\nSub-Industri:\nC33121 - Reparasi Mesin untuk Keperluan Umum \nC33122 - Reparasi Mesin untuk Keperluan Khusus "}, {"color": "#F8AD3C", "id": "C3313 - Reparasi Peralatan Elektronik \u0026 Optik", "label": "Reparasi Peralatan Elektronik\n\u0026 Optik\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reparasi Peralatan Elektronik dan Optik\n\nSub-Industri:\nC33131 - Reparasi Alat Ukur, Alat Uji, Alat Navigasi \u0026 Pengontrol\nC33132 - Reparasi Peralatan Iradiasi, Elektromedis \u0026 Elektroterapi \nC33133 - Reparasi Peralatan Fotografi \u0026 Optik "}, {"color": "#F8AD3C", "id": "C3314 - Reparasi Peralatan Listrik ", "label": "Reparasi Peralatan Listrik \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reparasi Peralatan Listrik \n\nSub-Industri:\nC33141 - Reparasi Motor Listrik, Generator \u0026 Transformator \nC33142 - Reparasi Baterai \u0026 Akumulator Listrik\nC33149 - Reparasi Peralatan Listrik Lainnya "}, {"color": "#F8AD3C", "id": "C3315 - Reparasi Alat Angkutan, Bukan Kendaraan Bermotor ", "label": "Reparasi Alat Angkutan,\nBukan Kendaraan Bermotor \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reparasi Alat Angkutan, Bukan Kendaraan Bermotor \n\nSub-Industri:\nC33151 - Reparasi Kapal, Perahu \u0026 Bangunan Terapung \nC33152 - Reparasi Lokomotif \u0026 Gerbong Kereta\nC33153 - Reparasi Pesawat Terbang \nC33159 - Reparasi Alat Angkutan Lainnya, Bukan Kendaraan Bermotor "}, {"color": "#F8AD3C", "id": "C3319 - Reparasi Peralatan Lainnya ", "label": "Reparasi Peralatan Lainnya \nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Reparasi Peralatan Lainnya "}, {"color": "#F8AD3C", "id": "C332 - Instalasi/Pemasangan Mesin \u0026 Peralatan Industri", "label": "Instalasi/Pemasangan Mesin\n\u0026 Peralatan Industri\nRp0.0 Tn", "shape": "dot", "size": 3.0, "title": "Instalasi/Pemasangan Mesin dan Peralatan Industri"}]);
                  edges = new vis.DataSet([{"from": "C - Sektor Manufaktur", "to": "C10 - Industri Makanan", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C101 - Industri Pengolahan Daging", "width": 0.1}, {"from": "C101 - Industri Pengolahan Daging", "to": "C1011 - Rumah Potong Daging Bukan Unggas", "width": 0.1}, {"from": "C101 - Industri Pengolahan Daging", "to": "C1012 - Rumah Potong Daging Unggas", "width": 0.1}, {"from": "C101 - Industri Pengolahan Daging", "to": "C1013 - Industri Pengolahan Produk Daging", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C102 - Industri Pengolahan Ikan \u0026 Biota Air", "width": 0.1}, {"from": "C102 - Industri Pengolahan Ikan \u0026 Biota Air", "to": "C1021 - Industri Pengolahan Ikan", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C1022 - Industri Pengolahan Ikan \u0026 Biota Air dalam Kaleng ", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C1029 - Industri Pengolahan \u0026 Pengawetan Biota Air Lainnya ", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C103 - Industri Pengolahan Buah \u0026 Sayuran ", "width": 0.1}, {"from": "C103 - Industri Pengolahan Buah \u0026 Sayuran ", "to": "C1031 - Industri Pengolahan Buah \u0026 Sayuran Diasinkan, Dilumatkan, Dikeringkan \u0026 Dibekukan", "width": 0.1}, {"from": "C103 - Industri Pengolahan Buah \u0026 Sayuran ", "to": "C1032 - Industri Pengolahan Buah \u0026 Sayuran dalam Kaleng ", "width": 0.1}, {"from": "C103 - Industri Pengolahan Buah \u0026 Sayuran ", "to": "C1033 - Industri Pengolahan Sari Buah \u0026 Sayuran", "width": 0.1}, {"from": "C103 - Industri Pengolahan Buah \u0026 Sayuran ", "to": "C1039 - Industri Pengolahan Buah \u0026 Sayuran Lainnya", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C104 - Industri Minyak Nabati \u0026 Hewani", "width": 0.1}, {"from": "C104 - Industri Minyak Nabati \u0026 Hewani", "to": "C1041 - Industri Minyak Nabati \u0026 Hewani (Bukan Kelapa \u0026 Kelapa Sawit)", "width": 0.1}, {"from": "C104 - Industri Minyak Nabati \u0026 Hewani", "to": "C1042 - Industri Kopra, Minyak Mentah, Minyak Goreng Kelapa \u0026 Pelet Kelapa", "width": 0.1}, {"from": "C104 - Industri Minyak Nabati \u0026 Hewani", "to": "C1043 - Industri Minyak Mentah \u0026 Minyak Goreng Kelapa Sawit", "width": 0.1}, {"from": "C104 - Industri Minyak Nabati \u0026 Hewani", "to": "C1049 - Industri Minyak Mentah \u0026 Lemak Nabati \u0026 Hewani Lainnya ", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C105 - Industri Pengolahan Susu \u0026 Es Krim", "width": 0.1}, {"from": "C105 - Industri Pengolahan Susu \u0026 Es Krim", "to": "C1051 - Industri Pengolahan Susu Bubuk \u0026 Susu Kental ", "width": 0.1}, {"from": "C105 - Industri Pengolahan Susu \u0026 Es Krim", "to": "C1052 - Industri Pengolahan Susu Bubuk \u0026 Susu Kental ", "width": 0.1}, {"from": "C105 - Industri Pengolahan Susu \u0026 Es Krim", "to": "C1053 - Industri Pengolahan Es Krim \u0026 Sejenisnya", "width": 0.1}, {"from": "C105 - Industri Pengolahan Susu \u0026 Es Krim", "to": "C1059 - Industri Pengolahan Produk dari Susu Lainnya ", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C106 - Industri Penggilingan Padi-Padian, Tepung \u0026 Pati", "width": 0.1}, {"from": "C106 - Industri Penggilingan Padi-Padian, Tepung \u0026 Pati", "to": "C1061 - Industri Penggilingan Serelia \u0026 Biji-Bijian Lainnya", "width": 0.1}, {"from": "C106 - Industri Penggilingan Padi-Padian, Tepung \u0026 Pati", "to": "C1062 - Industri Pati", "width": 0.1}, {"from": "C106 - Industri Penggilingan Padi-Padian, Tepung \u0026 Pati", "to": "C1063 - Industri Penggilingan dan Industri Tepung Beras \u0026 Jagung ", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C107 - Industri Makanan Lainnya", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1071 - Industri Produk Roti \u0026 Kue", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1072 - Industri Gula ", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1073 - Industri Kakao, Cokelat \u0026 Kembang Gula ", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1074 - Industri Makaroni, Mie \u0026 Sejenisnya ", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1075 - Industri Makanan \u0026 Masakan Olahan", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1076 - Industri Pengolahan Kopi, Teh \u0026 Herbal", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1077 - Industri Bumbu \u0026 Produk Masak Lainnya ", "width": 0.1}, {"from": "C107 - Industri Makanan Lainnya", "to": "C1079 - Industri Produk Makanan Lainnya", "width": 0.1}, {"from": "C10 - Industri Makanan", "to": "C108 - Industri Makanan Hewan", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C11 - Industri Minuman", "width": 0.1}, {"from": "C11 - Industri Minuman", "to": "C1101 - Industri Minuman Beralkohol Hasil Destilasi ", "width": 0.1}, {"from": "C11 - Industri Minuman", "to": "C1102 - Industri Minuman Beralkohol Hasil Fermentasi Anggur", "width": 0.1}, {"from": "C11 - Industri Minuman", "to": "C1103 - Industri Minuman Beralkohol Hasil Fermentasi Malt \u0026 Industri Malt ", "width": 0.1}, {"from": "C11 - Industri Minuman", "to": "C1104 - Industri Minuman Ringan ", "width": 0.1}, {"from": "C11 - Industri Minuman", "to": "C1105 - Industri Air Kemasan \u0026 Air Minum Isi Ulang ", "width": 0.1}, {"from": "C11 - Industri Minuman", "to": "C1109 - Industri Minuman Lainnya ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C12 - Industri Pengolahan Tembakau", "width": 0.1}, {"from": "C12 - Industri Pengolahan Tembakau", "to": "C1201 - Industri Rokok \u0026 Produk Tembakau Lainnya ", "width": 0.1}, {"from": "C12 - Industri Pengolahan Tembakau", "to": "C1209 - Industri Pengolahan Tembakau Lainnya ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C13 - Industri Tekstil", "width": 0.1}, {"from": "C13 - Industri Tekstil", "to": "C131 - Industri Pemintalan, Pertenunan \u0026 Penyempurnaan Tekstil ", "width": 0.1}, {"from": "C131 - Industri Pemintalan, Pertenunan \u0026 Penyempurnaan Tekstil ", "to": "C1311 - Industri Persiapan \u0026 Pemintalan Serat Tekstil ", "width": 0.1}, {"from": "C13 - Industri Tekstil", "to": "C1312 - Industri Pertenunan Tekstil ", "width": 0.1}, {"from": "C13 - Industri Tekstil", "to": "C1313 - Industri Penyempurnaan Tekstil", "width": 0.1}, {"from": "C13 - Industri Tekstil", "to": "C139 - Industri Tekstil Lainnya ", "width": 0.1}, {"from": "C139 - Industri Tekstil Lainnya ", "to": "C1391 - Industri Kain Rajutan \u0026 Sulaman ", "width": 0.1}, {"from": "C139 - Industri Tekstil Lainnya ", "to": "C1392 - Industri Pembuatan Barang Tekstil, Bukan Pakaian Jadi ", "width": 0.1}, {"from": "C13 - Industri Tekstil", "to": "C1393 - Industri Karpet \u0026 Permadani ", "width": 0.1}, {"from": "C13 - Industri Tekstil", "to": "C1394 - Industri Tali \u0026 Barang dari Tali", "width": 0.1}, {"from": "C13 - Industri Tekstil", "to": "C1399 - Industri Tekstil Lainnya Ytdl", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C14 - Industri Pakaian Jadi ", "width": 0.1}, {"from": "C14 - Industri Pakaian Jadi ", "to": "C141 - Industri Pakaian Jadi \u0026 Perlengkapannya", "width": 0.1}, {"from": "C141 - Industri Pakaian Jadi \u0026 Perlengkapannya", "to": "C1411 - Industri Pakaian Jadi (Bukan Penjahitan \u0026 Pembuatan Pakaian) ", "width": 0.1}, {"from": "C141 - Industri Pakaian Jadi \u0026 Perlengkapannya", "to": "C1412 - Penjahitan \u0026 Pembuatan Pakaian Sesuai Pesanan ", "width": 0.1}, {"from": "C141 - Industri Pakaian Jadi \u0026 Perlengkapannya", "to": "C1413 - Industri Perlengkapan Pakaian", "width": 0.1}, {"from": "C14 - Industri Pakaian Jadi ", "to": "C142 - Industri Pakaian Jadi \u0026 Barang dari Kulit Berbulu", "width": 0.1}, {"from": "C14 - Industri Pakaian Jadi ", "to": "C143 - Industri Pakaian Jadi Rajutan \u0026 Sulaman/Bordir", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C15 - Industri Kulit, Barang dari Kulit \u0026 Alas Kaki", "width": 0.1}, {"from": "C15 - Industri Kulit, Barang dari Kulit \u0026 Alas Kaki", "to": "C151 - Industri Kulit \u0026 Barang dari Kulit", "width": 0.1}, {"from": "C151 - Industri Kulit \u0026 Barang dari Kulit", "to": "C1511 - Industri Kulit \u0026 Kulit Komposisi", "width": 0.1}, {"from": "C151 - Industri Kulit \u0026 Barang dari Kulit", "to": "C1512 - Industri Barang dari Kulit, Koper, Tas Tangan \u0026 Sejenisnya", "width": 0.1}, {"from": "C15 - Industri Kulit, Barang dari Kulit \u0026 Alas Kaki", "to": "C152 - Industri Alas Kaki", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C16 - Industri Kayu, Gabus, Bambu, Rotan \u0026 Sejenisnya ", "width": 0.1}, {"from": "C16 - Industri Kayu, Gabus, Bambu, Rotan \u0026 Sejenisnya ", "to": "C161 - Industri Penggergajian Kayu, Rotan, Bambu \u0026 Sejenisnya ", "width": 0.1}, {"from": "C16 - Industri Kayu, Gabus, Bambu, Rotan \u0026 Sejenisnya ", "to": "C162 - Industri Barang dari Kayu \u0026 Sejenis Lainnya", "width": 0.1}, {"from": "C162 - Industri Barang dari Kayu \u0026 Sejenis Lainnya", "to": "C1621 - Industri Veneer, Kayu Lapis, Kayu Laminasi \u0026 Sejenisnya ", "width": 0.1}, {"from": "C162 - Industri Barang dari Kayu \u0026 Sejenis Lainnya", "to": "C1622 - Industri Barang Bangunan dari Kayu", "width": 0.1}, {"from": "C162 - Industri Barang dari Kayu \u0026 Sejenis Lainnya", "to": "C1623 - Industri Wadah dari Kayu ", "width": 0.1}, {"from": "C162 - Industri Barang dari Kayu \u0026 Sejenis Lainnya", "to": "C1629 - ndustri Barang Lainnya dari Kayu \u0026 Sejenisnya ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C17 - Industri Kertas \u0026 Barang dari Kertas", "width": 0.1}, {"from": "C17 - Industri Kertas \u0026 Barang dari Kertas", "to": "C1701 - Industri Bubur Kertas, Kertas \u0026 Papan Kertas ", "width": 0.1}, {"from": "C17 - Industri Kertas \u0026 Barang dari Kertas", "to": "C1702 - Industri Kertas, Papan Kertas \u0026 Wadah dari Kertas", "width": 0.1}, {"from": "C17 - Industri Kertas \u0026 Barang dari Kertas", "to": "C1709 - Industri Barang dari Kertas \u0026 Papan Kertas Lainnya ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C18 -  Industri Pencetakan \u0026 Reproduksi Media Rekaman", "width": 0.1}, {"from": "C18 -  Industri Pencetakan \u0026 Reproduksi Media Rekaman", "to": "C181 - Industri Pencetakan \u0026 Kegiatan Ybdi", "width": 0.1}, {"from": "C181 - Industri Pencetakan \u0026 Kegiatan Ybdi", "to": "C1811 - Industri Pencetakan ", "width": 0.1}, {"from": "C181 - Industri Pencetakan \u0026 Kegiatan Ybdi", "to": "C1812 - Kegiatan Jasa Penunjang Pencetakan ", "width": 0.1}, {"from": "C18 -  Industri Pencetakan \u0026 Reproduksi Media Rekaman", "to": "C182 - Reproduksi Media Rekaman", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C19 - Industri Produk dari Batu Bara \u0026 Pengilangan Minyak Bumi ", "width": 0.1}, {"from": "C19 - Industri Produk dari Batu Bara \u0026 Pengilangan Minyak Bumi ", "to": "C191 - Industri Produk dari Batu Bara", "width": 0.1}, {"from": "C19 - Industri Produk dari Batu Bara \u0026 Pengilangan Minyak Bumi ", "to": "C192 - Industri Produk Pengilangan Minyak Bumi", "width": 0.1}, {"from": "C192 - Industri Produk Pengilangan Minyak Bumi", "to": "C1921 - Industri Bahan Bakar \u0026 Minyak Pelumas", "width": 0.1}, {"from": "C192 - Industri Produk Pengilangan Minyak Bumi", "to": "C1929 - Industri Produk Pengilangan Minyak Bumi Lainnya ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C20 - Industri Bahan Kimia \u0026 Barang dari Bahan Kimia", "width": 0.1}, {"from": "C20 - Industri Bahan Kimia \u0026 Barang dari Bahan Kimia", "to": "C201 - Industri Bahan Kimia", "width": 0.1}, {"from": "C201 - Industri Bahan Kimia", "to": "C2011 - Industri Kimia Dasar", "width": 0.1}, {"from": "C201 - Industri Bahan Kimia", "to": "C2012 - Industri Pupuk \u0026 Bahan Senyawa Nitrogen ", "width": 0.1}, {"from": "C201 - Industri Bahan Kimia", "to": "C2013 - Industri Plastik \u0026 Karet Buatan dalam Bentuk Dasar ", "width": 0.1}, {"from": "C20 - Industri Bahan Kimia \u0026 Barang dari Bahan Kimia", "to": "C202 - Industri Barang Kimia Lainnya", "width": 0.1}, {"from": "C202 - Industri Barang Kimia Lainnya", "to": "C2021 - Industri Pestisida \u0026 Produk Agrokimia Lainnya ", "width": 0.1}, {"from": "C202 - Industri Barang Kimia Lainnya", "to": "C2022 - Industri Cat \u0026 Tinta Cetak, Pernis \u0026 Bahan Pelapisan Sejenisnya \u0026 Lak ", "width": 0.1}, {"from": "C202 - Industri Barang Kimia Lainnya", "to": "C2023 - Industri Sabun \u0026 Deterjen, Bahan Pembersih \u0026 Pengilap, Parfum \u0026 Kosmetik ", "width": 0.1}, {"from": "C202 - Industri Barang Kimia Lainnya", "to": "C2029 - Industri Barang Kimia Lainnya Ytdl ", "width": 0.1}, {"from": "C20 - Industri Bahan Kimia \u0026 Barang dari Bahan Kimia", "to": "C203 - Industri Serat Buatan", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C21 - Industri Farmasi, Produk Obat Kimia \u0026 Obat Tradisional", "width": 0.1}, {"from": "C21 - Industri Farmasi, Produk Obat Kimia \u0026 Obat Tradisional", "to": "C2101 - Industri Farmasi \u0026 Produk Obat Kimia ", "width": 0.1}, {"from": "C21 - Industri Farmasi, Produk Obat Kimia \u0026 Obat Tradisional", "to": "C2102 - Industri Obat Tradisional ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C22 - Industri Karet, Barang dari Karet \u0026 Plastik", "width": 0.1}, {"from": "C22 - Industri Karet, Barang dari Karet \u0026 Plastik", "to": "C221 - Industri Karet \u0026 Barang dari Karet", "width": 0.1}, {"from": "C221 - Industri Karet \u0026 Barang dari Karet", "to": "C2211 - Industri Ban \u0026 Vulkanisir Ban ", "width": 0.1}, {"from": "C221 - Industri Karet \u0026 Barang dari Karet", "to": "C2212 - Industri Pengasapan, Remilling \u0026 Karet Remah ", "width": 0.1}, {"from": "C221 - Industri Karet \u0026 Barang dari Karet", "to": "C2219 - Industri Barang dari Karet Lainnya ", "width": 0.1}, {"from": "C22 - Industri Karet, Barang dari Karet \u0026 Plastik", "to": "C222 - Industri Barang dari Plastik", "width": 0.1}, {"from": "C222 - Industri Barang dari Plastik", "to": "C2221 - Industri Barang dari Plastik untuk Bangunan", "width": 0.1}, {"from": "C222 - Industri Barang dari Plastik", "to": "C2222 - Industri Barang dari Plastik untuk Pengemasan ", "width": 0.1}, {"from": "C222 - Industri Barang dari Plastik", "to": "C2223 - Industri Pipa Plastik \u0026 Perlengkapannya ", "width": 0.1}, {"from": "C222 - Industri Barang dari Plastik", "to": "C2229 - Industri Barang dari Plastik Lainnya ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C23 - Industri Barang Galian Bukan Logam", "width": 0.1}, {"from": "C23 - Industri Barang Galian Bukan Logam", "to": "C231 - Industri Kaca \u0026 Barang dari Kaca", "width": 0.1}, {"from": "C231 - Industri Kaca \u0026 Barang dari Kaca", "to": "C2311 - Industri Kaca", "width": 0.1}, {"from": "C231 - Industri Kaca \u0026 Barang dari Kaca", "to": "C2312 - Industri Barang dari Kaca", "width": 0.1}, {"from": "C23 - Industri Barang Galian Bukan Logam", "to": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "width": 0.1}, {"from": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "to": "C2391 - Industri Barang Refraktori", "width": 0.1}, {"from": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "to": "C2392 - Industri Bahan Bangunan dari Tanah Liat/Keramik ", "width": 0.1}, {"from": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "to": "C2393 - Industri Barang Tanah Liat/Keramik \u0026 Porselen", "width": 0.1}, {"from": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "to": "C2394 - Industri Semen, Kapur \u0026 Gips ", "width": 0.1}, {"from": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "to": "C2395 - Industri Barang dari Semen, Kapur, Gips \u0026 Asbes ", "width": 0.1}, {"from": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "to": "C2396 - Industri Barang dari Batu ", "width": 0.1}, {"from": "C239 - Industri Barang Galian Bukan Logam Lainnya ", "to": "C2399 - Industri Barang Galian Bukan Logam Lainnya Ytdl ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C24 - Industri Logam Dasar", "width": 0.1}, {"from": "C24 - Industri Logam Dasar", "to": "C241 - Industri Logam Dasar Besi \u0026 Baja", "width": 0.1}, {"from": "C241 - Industri Logam Dasar Besi \u0026 Baja", "to": "C2410 - Industri Logam Dasar Besi \u0026 Baja", "width": 0.1}, {"from": "C24 - Industri Logam Dasar", "to": "C242 - Industri Logam Dasar Mulia \u0026 Logam Dasar Bukan Besi Lainnya ", "width": 0.1}, {"from": "C242 - Industri Logam Dasar Mulia \u0026 Logam Dasar Bukan Besi Lainnya ", "to": "C2420 - Industri Logam Dasar Mulia \u0026 Logam Dasar Bukan Besi Lainnya ", "width": 0.1}, {"from": "C24 - Industri Logam Dasar", "to": "C243 - Industri Pengecoran Logam", "width": 0.1}, {"from": "C243 - Industri Pengecoran Logam", "to": "C2431 - Industri Pengecoran Besi \u0026 Baja", "width": 0.1}, {"from": "C243 - Industri Pengecoran Logam", "to": "C2432 - Industri Pengecoran Logam Bukan Besi \u0026 Baja ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C25 - Industri Barang Logam, Bukan Mesin \u0026 Peralatannya", "width": 0.1}, {"from": "C25 - Industri Barang Logam, Bukan Mesin \u0026 Peralatannya", "to": "C251 - Industri Barang Logam Siap Pasang", "width": 0.1}, {"from": "C251 - Industri Barang Logam Siap Pasang", "to": "C2511 - Industri Barang Logam Siap Pasang untuk Bangunan ", "width": 0.1}, {"from": "C251 - Industri Barang Logam Siap Pasang", "to": "C2512 - Industri Tangki, Tandon Air \u0026 Wadah dari Logam ", "width": 0.1}, {"from": "C251 - Industri Barang Logam Siap Pasang", "to": "C2513 - Industri Generator Uap, Bukan Ketel Pemanas ", "width": 0.1}, {"from": "C25 - Industri Barang Logam, Bukan Mesin \u0026 Peralatannya", "to": "C252 - Industri Senjata \u0026 Amunisi", "width": 0.1}, {"from": "C25 - Industri Barang Logam, Bukan Mesin \u0026 Peralatannya", "to": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "width": 0.1}, {"from": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "to": "C2591 - Industri Penempaan, Pengepresan, Pencetakan \u0026 Pembentukan Logam", "width": 0.1}, {"from": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "to": "C2592 - Jasa Industri untuk Berbagai Pengerjaan Khusus Barang Logam", "width": 0.1}, {"from": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "to": "C2593 - Industri Alat Potong, Perkakas Tangan \u0026 Peralatan Umum ", "width": 0.1}, {"from": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "to": "C2594 - Industri Ember, Kaleng, Drum \u0026 Wadah Sejenis dari Logam ", "width": 0.1}, {"from": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "to": "C2595 - Industri Barang dari Kawat \u0026 Paku, Mur \u0026 Baut", "width": 0.1}, {"from": "C259 - Industri Barang Logam Lainnya \u0026 Jasa Pembuatan Barang Logam ", "to": "C2599 - Industri Barang Logam Lainnya", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C261 - Industri Komponen \u0026 Papan Elektronik", "width": 0.1}, {"from": "C261 - Industri Komponen \u0026 Papan Elektronik", "to": "C2611 - Industri Tabung Elektron \u0026 Konektor Elektronik ", "width": 0.1}, {"from": "C261 - Industri Komponen \u0026 Papan Elektronik", "to": "C2612 - Industri Semi Konduktor \u0026 Komponen Elektronik Lainnya ", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C262 - Industri Komputer \u0026 Perlengkapannya", "width": 0.1}, {"from": "C262 - Industri Komputer \u0026 Perlengkapannya", "to": "C2621 - Industri Komputer \u0026 Perakitan Komputer", "width": 0.1}, {"from": "C262 - Industri Komputer \u0026 Perlengkapannya", "to": "C2622 - Industri Perlengkapan Komputer", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C263 - Industri Peralatan Komunikasi", "width": 0.1}, {"from": "C263 - Industri Peralatan Komunikasi", "to": "C2631 - Industri Peralatan Telepon \u0026 Faksimili ", "width": 0.1}, {"from": "C263 - Industri Peralatan Komunikasi", "to": "C2632 - Industri Peralatan Komunikasi Tanpa Kabel", "width": 0.1}, {"from": "C263 - Industri Peralatan Komunikasi", "to": "C2639 - Industri Peralatan Komunikasi Lainnya", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C264 - Industri Peralatan Audio \u0026 Video Elektronik", "width": 0.1}, {"from": "C264 - Industri Peralatan Audio \u0026 Video Elektronik", "to": "C2641 - Industri Televisi \u0026 Perakitan Televisi", "width": 0.1}, {"from": "C264 - Industri Peralatan Audio \u0026 Video Elektronik", "to": "C2642 - Industri Peralatan Perekam Audio \u0026 Video", "width": 0.1}, {"from": "C264 - Industri Peralatan Audio \u0026 Video Elektronik", "to": "C2649 - Industri Peralatan Audio \u0026 Video Elektronik Lainnya", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C265 - Industri Alat Ukur, Alat Uji, Alat Navigasi \u0026 Alat Ukur Waktu ", "width": 0.1}, {"from": "C265 - Industri Alat Ukur, Alat Uji, Alat Navigasi \u0026 Alat Ukur Waktu ", "to": "C2651 - Industri Alat Ukur, Alat Uji, Alat Navigasi \u0026 Kontrol ", "width": 0.1}, {"from": "C265 - Industri Alat Ukur, Alat Uji, Alat Navigasi \u0026 Alat Ukur Waktu ", "to": "C2652 - Industri Alat Ukur Waktu", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C266 - Industri Peralatan Iradiasi, Elektromedikal \u0026 Elektroterapi ", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C267 - Industri Peralatan Fotografi \u0026 Instrumen Optik Bukan Kaca Mata ", "width": 0.1}, {"from": "C267 - Industri Peralatan Fotografi \u0026 Instrumen Optik Bukan Kaca Mata ", "to": "C2671 - Industri Peralatan Fotografi", "width": 0.1}, {"from": "C267 - Industri Peralatan Fotografi \u0026 Instrumen Optik Bukan Kaca Mata ", "to": "C2679 - Industri Peralatan Fotografi \u0026 Instrumen Optik Lainnya", "width": 0.1}, {"from": "C26 - Industri Komputer, Barang Elektronik \u0026 Optik", "to": "C268 - Industri Media Magnetik \u0026 Media Optik", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C27 - Industri Peralatan Listrik", "width": 0.1}, {"from": "C27 - Industri Peralatan Listrik", "to": "C271 - Industri Motor Listrik, Generator, Transformator \u0026 Pendistribusian Listrik", "width": 0.1}, {"from": "C271 - Industri Motor Listrik, Generator, Transformator \u0026 Pendistribusian Listrik", "to": "C2711 - Industri Motor Listrik, Generator \u0026 Transformator ", "width": 0.1}, {"from": "C271 - Industri Motor Listrik, Generator, Transformator \u0026 Pendistribusian Listrik", "to": "C2712 - Industri Peralatan Pengontrol \u0026 Pendistribusian Listrik ", "width": 0.1}, {"from": "C27 - Industri Peralatan Listrik", "to": "C272 - Industri Batu Baterai \u0026 Akumulator Listrik", "width": 0.1}, {"from": "C27 - Industri Peralatan Listrik", "to": "C273 - Industri Kabel \u0026 Perlengkapannya", "width": 0.1}, {"from": "C273 - Industri Kabel \u0026 Perlengkapannya", "to": "C2731 - Industri Kabel Serat Optik", "width": 0.1}, {"from": "C273 - Industri Kabel \u0026 Perlengkapannya", "to": "C2732 - Industri Kabel Listrik \u0026 Elektronik Lainnya ", "width": 0.1}, {"from": "C273 - Industri Kabel \u0026 Perlengkapannya", "to": "C2733 - Industri Perlengkapan Kabel", "width": 0.1}, {"from": "C27 - Industri Peralatan Listrik", "to": "C274 - Industri Peralatan Penerangan Listrik ", "width": 0.1}, {"from": "C27 - Industri Peralatan Listrik", "to": "C275 - Industri Peralatan Rumah Tangga", "width": 0.1}, {"from": "C275 - Industri Peralatan Rumah Tangga", "to": "C2751 - Industri Peralatan Listrik Rumah Tangga ", "width": 0.1}, {"from": "C275 - Industri Peralatan Rumah Tangga", "to": "C2752 - Industri Peralatan Elektrotermal Rumah Tangga ", "width": 0.1}, {"from": "C275 - Industri Peralatan Rumah Tangga", "to": "C2753 - Industri Peralatan Pemanas \u0026 Masak Bukan Listrik Rumah Tangga ", "width": 0.1}, {"from": "C27 - Industri Peralatan Listrik", "to": "C279 - Industri Peralatan Listrik Lainnya", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C28 - Industri Mesin \u0026 Perlengkapan Lainnya", "width": 0.1}, {"from": "C28 - Industri Mesin \u0026 Perlengkapan Lainnya", "to": "C281 - Industri Mesin untuk Keperluan Umum", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2811 - Industri Mesin \u0026 Turbin, Bukan Mesin Pesawat \u0026 Kendaraan Bermotor", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2812 - Industri Peralatan Tenaga Zat Cair \u0026 Gas ", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2813 - Industri Pompa Lainnya, Kompresor, Kran \u0026 Klep/Katup ", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2814 - Industri Bearing, Roda Gigi \u0026 Elemen Penggerak Mesin ", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2815 - Industri Oven, Perapian \u0026 Tungku Pembakar", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2816 - Industri Alat Pengangkat \u0026 Pemindah", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2817 - Industri Mesin \u0026 Peralatan Kantor (Bukan Komputer)", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2818 - Industri Perkakas Tangan yang Digerakkan Tenaga ", "width": 0.1}, {"from": "C281 - Industri Mesin untuk Keperluan Umum", "to": "C2819 - Industri Mesin untuk Keperluan Umum Lainnya ", "width": 0.1}, {"from": "C28 - Industri Mesin \u0026 Perlengkapan Lainnya", "to": "C282 - Industri Mesin untuk Keperluan Khusus", "width": 0.1}, {"from": "C282 - Industri Mesin untuk Keperluan Khusus", "to": "C2821 - Industri Mesin Pertanian \u0026 Kehutanan", "width": 0.1}, {"from": "C282 - Industri Mesin untuk Keperluan Khusus", "to": "C2822 - Industri Mesin \u0026 Perkakas Mesin", "width": 0.1}, {"from": "C282 - Industri Mesin untuk Keperluan Khusus", "to": "C2823 - Industri Mesin Metalurgi ", "width": 0.1}, {"from": "C282 - Industri Mesin untuk Keperluan Khusus", "to": "C2824 - Industri Mesin Penambangan, Penggalian \u0026 Konstruksi ", "width": 0.1}, {"from": "C282 - Industri Mesin untuk Keperluan Khusus", "to": "C2825 - Industri Mesin Pengolahan Makanan, Minuman \u0026 Tembakau ", "width": 0.1}, {"from": "C282 - Industri Mesin untuk Keperluan Khusus", "to": "C2826 - Industri Mesin Tekstil, Pakaian Jadi \u0026 Produk Kulit ", "width": 0.1}, {"from": "C282 - Industri Mesin untuk Keperluan Khusus", "to": "C2829 - Industri Mesin Keperluan Khusus Lainnya ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C29 - Industri Kendaraan Bermotor, Trailer \u0026 Semi Trailer", "width": 0.1}, {"from": "C29 - Industri Kendaraan Bermotor, Trailer \u0026 Semi Trailer", "to": "C291 - Industri Kendaraan Bermotor Roda Empat atau Lebih", "width": 0.1}, {"from": "C29 - Industri Kendaraan Bermotor, Trailer \u0026 Semi Trailer", "to": "C292 - Industri Karoseri Kendaraan Bermotor Roda Empat atau Lebih", "width": 0.1}, {"from": "C29 - Industri Kendaraan Bermotor, Trailer \u0026 Semi Trailer", "to": "C293 - Industri Suku Cadang \u0026 Aksesori Kendaraan Bermotor Roda Empat atau Lebih ", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C30 - Industri Alat Angkutan Lainnya", "width": 0.1}, {"from": "C30 - Industri Alat Angkutan Lainnya", "to": "C301 - Industri Pembuatan Kapal \u0026 Perahu", "width": 0.1}, {"from": "C301 - Industri Pembuatan Kapal \u0026 Perahu", "to": "C3011 - Industri Pembuatan Kapal, Perahu \u0026 Bangunan Terapung ", "width": 0.1}, {"from": "C301 - Industri Pembuatan Kapal \u0026 Perahu", "to": "C3012 - Industri Pembuatan Kapal \u0026 Perahu untuk Wisata, Rekreasi \u0026 Olahraga ", "width": 0.1}, {"from": "C30 - Industri Alat Angkutan Lainnya", "to": "C302 - Industri Lokomotif \u0026 Gerbong Kereta", "width": 0.1}, {"from": "C30 - Industri Alat Angkutan Lainnya", "to": "C303 - Industri Pesawat Terbang \u0026 Perlengkapannya", "width": 0.1}, {"from": "C30 - Industri Alat Angkutan Lainnya", "to": "C304 - Industri Kendaraan Perang", "width": 0.1}, {"from": "C30 - Industri Alat Angkutan Lainnya", "to": "C309 - Industri Alat Angkutan Lainnya Ytdl", "width": 0.1}, {"from": "C309 - Industri Alat Angkutan Lainnya Ytdl", "to": "C3091 - Industri Sepeda Motor ", "width": 0.1}, {"from": "C309 - Industri Alat Angkutan Lainnya Ytdl", "to": "C3092 - Industri Sepeda \u0026 Kursi Roda ", "width": 0.1}, {"from": "C309 - Industri Alat Angkutan Lainnya Ytdl", "to": "C3099 - Industri Alat Angkutan Lainnya Ytdl", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C31 - Industri Furnitur", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C32 - Industri Pengolahan Lainnya", "width": 0.1}, {"from": "C32 - Industri Pengolahan Lainnya", "to": "C321 - Industri Barang Perhiasan \u0026 Barang Berharga", "width": 0.1}, {"from": "C321 - Industri Barang Perhiasan \u0026 Barang Berharga", "to": "C3211 - Industri Perhiasan \u0026 Barang Sejenis ", "width": 0.1}, {"from": "C321 - Industri Barang Perhiasan \u0026 Barang Berharga", "to": "C3212 - Industri Perhiasan Imitasi \u0026 Barang Sejenis ", "width": 0.1}, {"from": "C32 - Industri Pengolahan Lainnya", "to": "C322 - Industri Alat Musik", "width": 0.1}, {"from": "C32 - Industri Pengolahan Lainnya", "to": "C323 - Industri Alat Olahraga", "width": 0.1}, {"from": "C32 - Industri Pengolahan Lainnya", "to": "C324 - Industri Alat Permainan \u0026 Mainan Anak-Anak", "width": 0.1}, {"from": "C32 - Industri Pengolahan Lainnya", "to": "C325 - Industri Peralatan Kedokteran serta Perlengkapannya", "width": 0.1}, {"from": "C32 - Industri Pengolahan Lainnya", "to": "C329 - Industri Pengolahan Lainnya", "width": 0.1}, {"from": "C - Sektor Manufaktur", "to": "C33 - Reparasi \u0026 Pemasangan Mesin \u0026 Peralatan", "width": 0.1}, {"from": "C33 - Reparasi \u0026 Pemasangan Mesin \u0026 Peralatan", "to": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "width": 0.1}, {"from": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "to": "C3311 - Reparasi Produk Logam Pabrikasi ", "width": 0.1}, {"from": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "to": "C3312 - Reparasi Mesin", "width": 0.1}, {"from": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "to": "C3313 - Reparasi Peralatan Elektronik \u0026 Optik", "width": 0.1}, {"from": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "to": "C3314 - Reparasi Peralatan Listrik ", "width": 0.1}, {"from": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "to": "C3315 - Reparasi Alat Angkutan, Bukan Kendaraan Bermotor ", "width": 0.1}, {"from": "C331 - Reparasi Produk Logam Pabrikasi, Mesin \u0026 Peralatan ", "to": "C3319 - Reparasi Peralatan Lainnya ", "width": 0.1}, {"from": "C33 - Reparasi \u0026 Pemasangan Mesin \u0026 Peralatan", "to": "C332 - Instalasi/Pemasangan Mesin \u0026 Peralatan Industri", "width": 0.1}]);

                  nodeColors = {};
                  allNodes = nodes.get({ returnType: "Object" });
                  for (nodeId in allNodes) {
                    nodeColors[nodeId] = allNodes[nodeId].color;
                  }
                  allEdges = edges.get({ returnType: "Object" });
                  // adding nodes and edges to the graph
                  data = {nodes: nodes, edges: edges};

                  var options = {"nodes": {"borderWidth": 1, "borderWidthSelected": 2, "opacity": 100, "font": {"strokeWidth": 2}, "size": 25}, "edges": {"arrows": {"to": {"enabled": true}}, "color": {"inherit": true}, "selfReferenceSize": 20, "selfReference": {"angle": 0.7853981633974483}, "smooth": {"forceDirection": "none"}}, "physics": {"barnesHut": {"springLength": 100}, "minVelocity": 0.75}};

                  


                  

                  network = new vis.Network(container, data, options);

                  

                  
                    network.on("selectNode", neighbourhoodHighlight);
                  

                  


                  
                      network.on("stabilizationProgress", function(params) {
                          document.getElementById('loadingBar').removeAttribute("style");
                          var maxWidth = 496;
                          var minWidth = 20;
                          var widthFactor = params.iterations/params.total;
                          var width = Math.max(minWidth,maxWidth * widthFactor);
                          document.getElementById('bar').style.width = width + 'px';
                          document.getElementById('text').innerHTML = Math.round(widthFactor*100) + '%';
                      });
                      network.once("stabilizationIterationsDone", function() {
                          document.getElementById('text').innerHTML = '100%';
                          document.getElementById('bar').style.width = '496px';
                          document.getElementById('loadingBar').style.opacity = 0;
                          // really clean the dom element
                          setTimeout(function () {document.getElementById('loadingBar').style.display = 'none';}, 500);
                      });
                  

                  return network;

              }
              drawGraph();
        </script>
    </body>
</html>