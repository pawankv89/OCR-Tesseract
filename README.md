# OCR-Tesseract

## A Native iOS Text recognition app, converts "Image To Text" using Tesseract Framework.


Added Some screens here.

![](https://github.com/pawankv89/OCR-Tesseract/blob/master/images/screen_1.png)


## Usage

#### Controller

```swift


import UIKit
import TesseractOCR

struct ReadItem {
    var title: String = ""
}

class ViewController: UIViewController {
    
    @IBOutlet weak var chooseImageView: UIImageView!
    @IBOutlet weak var activityIndicatorView: UIActivityIndicatorView!
    @IBOutlet var tableView: UITableView!
    
    //Declare Local Array here
    var wordlists: Array <ReadItem> = Array <ReadItem>()

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        
        //daynamic Height Set
        self.tableView.estimatedRowHeight = 1 //Use less when recived data No 0 otherwise not set cell size default
        self.tableView.rowHeight = UITableView.automaticDimension
        
        let images = ["Pawan_en.png","Pawan_de.png","Pawan_fr.png","Pawan_jp.png","Pawan_ru.png"]
        
        recognizeImageWithTesseract(image: UIImage(named: images[0])!)
    }

    func recognizeImageWithTesseract(image: UIImage) -> () {
        
        //Loader
        self.activityIndicatorView.startAnimating()
        self.activityIndicatorView.isHidden = false
        
        self.chooseImageView.image = image
        
        self.wordlists.removeAll()
        self.wordlists = []
        
        // 1 eng+rus
        if let tesseract = G8Tesseract(language: "eng") {
          // 2
          tesseract.engineMode = .tesseractCubeCombined
          // 3
          tesseract.pageSegmentationMode = .auto
          // 4
          tesseract.image = image
          // 5
          tesseract.recognize()
          // 6
          //textView.text = tesseract.recognizedText
          print("recognizedText:- ", tesseract.recognizedText)
           
            if tesseract.recognizedText == nil { return }

            self.wordlists.append(ReadItem.init(title: tesseract.recognizedText!))
            
            DispatchQueue.main.async {
               
             self.tableView.reloadData()
             
             //Loader
             self.activityIndicatorView.stopAnimating()
             self.activityIndicatorView.isHidden = true
            
            }
            
        }
    }
}


//#MARK:- UITableView DataSource & Delegate

extension ViewController: UITableViewDataSource, UITableViewDelegate {
    
    func numberOfSections(in tableView: UITableView) -> Int {
           return 1
    }
    //PK-New-Added
    func tableView(_ tableView: UITableView, estimatedHeightForRowAt indexPath: IndexPath) -> CGFloat {
        return UITableView.automaticDimension
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int
    {
        if self.wordlists.count > 0 {
            return self.wordlists.count
        }
       return 0
    }
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
    {
        let cellIdentifier: String = "ReadCell"
        var cell : ReadCell? = tableView.dequeueReusableCell(withIdentifier: cellIdentifier) as? ReadCell
        if (cell == nil)
        {
            let nib: Array = Bundle.main.loadNibNamed(cellIdentifier, owner: self, options: nil)!
            cell = nib[0] as? ReadCell
        }
        
        let readItem: ReadItem  = self.wordlists[indexPath.row]
        
        //cell?.titleLabel.text? = NSLocalizedString(readItem.title, comment: "")
        cell?.titleLabel.text? = readItem.title
        
        //Cell Selection
        cell?.selectionStyle = .none
            
        return cell!
    }
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)
    {
        let readItem: ReadItem  = self.wordlists[indexPath.row]
        print("readItem:- ", readItem)
    }
}


```

## Requirements

### Build

Xcode Version 11.3 (11C29), iOS 13.2.0 SDK

## License

This code is distributed under the terms and conditions of the [MIT license](LICENSE).

## Change-log

A brief summary of each this release can be found in the [CHANGELOG](CHANGELOG.mdown). 

