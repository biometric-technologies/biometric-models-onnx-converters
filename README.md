![](.github/readme/banner.svg)

# Biometric models ONNX converters

Two Jupyter notebooks that convert the neural networks behind Iriscan's face pipeline to ONNX: the FaceNet face embedding model and the face liveness model. ONNX (Open Neural Network Exchange) is a file format for trained models that is not tied to one framework. A model saved as `.onnx` can be run by ONNX Runtime on Windows, Linux, macOS, Android and iOS, from C++, C#, Java, Python or JavaScript, without installing PyTorch or TensorFlow. Converting once lets the same model run wherever the app lives.

## What it does

- `convert_facenet_to_onnx.ipynb` installs `onnx`, clones [facenet-pytorch](https://github.com/timesler/facenet-pytorch), loads `InceptionResnetV1` with the CASIA-WebFace pretrained weights and exports it with `torch.onnx.export` for a 1 x 3 x 160 x 160 input to `facenet.onnx`. The output is a 512-number embedding, L2-normalized.
- `convert_liveness_to_onnx.ipynb` downloads `model.tflite` from [liveness-detection-model release v0.2.0](https://github.com/biometric-technologies/liveness-detection-model/releases/tag/v0.2.0) and converts it with `tf2onnx` (`python -m tf2onnx.convert --tflite model.tflite --output model.onnx`, opset 15) to `model.onnx`. The input is N x 224 x 224 x 3 and the output a single sigmoid value.
- Both notebooks load the result with `onnx.checker.check_model` and print the graph, so you can confirm the conversion is valid.

## Why convert

The [Biometric SDK](https://github.com/biometric-technologies/biometric-sdk) runs these models as TensorFlow Lite files on JVM, Android and iOS. The FaceNet weights come from a PyTorch project and the liveness model is trained in Keras, so each one is locked to its own runtime. ONNX gives both models one common format that runs through ONNX Runtime in environments where TensorFlow Lite is not an option, such as desktop and server applications. The saved notebook outputs show the versions used: onnx 1.15.0, tf2onnx 1.15.1 with TensorFlow 2.14.0, Python 3.10.

## How to run

Open a notebook in Jupyter or Google Colab and run the cells from top to bottom; each notebook installs what it needs with `pip` and `git clone`. The FaceNet notebook needs PyTorch, which Colab already provides. The converted files are written to the working directory of the cloned project (`facenet-pytorch/facenet.onnx` and `tensorflow-onnx/model.onnx`).

Keep in mind that the input preprocessing is not part of the model: FaceNet from facenet-pytorch expects 160 x 160 RGB input in channels-first order, and the liveness model expects a 224 x 224 RGB image scaled to [0, 1] in channels-last order, as documented in [liveness-detection-model](https://github.com/biometric-technologies/liveness-detection-model).

## Related repositories

- [liveness-detection-model](https://github.com/biometric-technologies/liveness-detection-model): training code and `.tflite` releases of the liveness model.
- [facenet-recognition-model](https://github.com/biometric-technologies/facenet-recognition-model): reserved for the FaceNet model.
- [tensorflow-facenet-model-test](https://github.com/biometric-technologies/tensorflow-facenet-model-test): script to test a FaceNet TensorFlow Lite model on your photos.
- [biometric-sdk](https://github.com/biometric-technologies/biometric-sdk): the SDK that uses these models in TensorFlow Lite form.

## License

License: not yet specified. The repository has no license file. The FaceNet weights come from facenet-pytorch and the liveness model from liveness-detection-model (MIT); their terms apply to the converted files.

## Contact

info@iriscan.com, https://www.iriscan.com
