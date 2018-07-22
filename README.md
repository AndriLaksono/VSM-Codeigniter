# VSM Codeigniter (Cosine Measurement)
Module VSM untuk membantu pencarian pada codeigniter menggunakan Information retrieval berbahasa indonesia.

## Setting Up
Untuk menggunakan model ini anda harus melakukan beberapa langkah setup berikut.
* Import table [tb_katadasar.sql](https://github.com/AndriLaksono/VSM-Codeigniter/blob/master/tb_katadasar.sql) ke database anda.
* Letakan **VSM.php** dan **Preprocessing.php** dibawah folder *application/libraries*
```
-application
---libraries
-------VSM.php
-------Preprocessing.php
```

### Penggunaan
Setelah melakukan setup anda dapat menggunakan pada controller.
Berikut contoh penggunaannya:

**Penggunaan di controller**
```
<?php

defined('BASEPATH') OR exit('No direct script access allowed');

class HomeController extends CI_Controller {

    
    public function __construct()
    {
        parent::__construct();
        //Do your magic here
        $this->load->model('BlogModel');
        $this->load->library('Preprocessing');
        $this->load->library('VSM');
    }

    public function index()
    {
        $this->load->view('V_pencarian');
    }

    public function cari()
    {
        $cari = $this->input->post('cari');
        
        // STEP 1 == PREPROCESSING
        $query = $this->preprocessing::preprocess($cari);

        // STEP 2 == GET DATA & MAKE TO ARRAY
        $dokumen = $this->BlogModel->get_data(); // return object
        $arrayDokumen = [];
        foreach ($dokumen as $doc) {
            $arrayDoc = [
                "id_doc"    => $doc->id_kriminal,
                "dokumen"   => implode(" ", $this->preprocessing::preprocess($doc->deskripsi))
            ];
            array_push($arrayDokumen, $arrayDoc);
        }
        
        // STEP 3 == VSM 
        $rank = $this->vsm->get_rank($query, $arrayDokumen);

        die(var_dump($rank));
    }

}
```

Contoh Model CI kami
```
<?php


defined('BASEPATH') OR exit('No direct script access allowed');

class BlogModel extends CI_Model {

    
    public function __construct()
    {
        parent::__construct();
    }

    public function get_data()
    {
        $data = $this->db->get('blog')->result();
        return $data;
    }
    

}
```

## Authors

* **Andri Laksono** - *Programmer* - [Satria Tech](https://satriatech.com)

## License

This project is licensed under the MIT License
