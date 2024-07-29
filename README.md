
# Auto Cropping Script (BasicAI, CSV)
## Description
This project provides a script to process a CSV file containing image annotations and crop images based on the bounding box (bbox) information. The cropped images are then saved in a structured directory hierarchy based on class name, shape type, occlusion level, and orientation.

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [License](#license)

## Installation
Install [PyCharm Community Edition](https://www.jetbrains.com/pycharm/download/?section=windows)

## Usage
To use the script, you need to provide the path to the CSV file, the folder containing images, and the output folder where the cropped images will be saved.

    1. Update the csv_file_path, image_folder_path, and output_folder_path variables in the script with the appropriate paths.
    2. Run the script:

### Example
Here is an example of how to use the script:

    import os
    import cv2


    def crop_bbox(image_path, bbox):
        image = cv2.imread(image_path)
        xmin, ymin, xmax, ymax = bbox
        ymin = int(ymin // 1)
        xmin = int(xmin // 1)
        ymax = int(ymax // 1)
        xmax = int(xmax // 1)
        ymax = min(ymax, image.shape[0])
        xmax = min(xmax, image.shape[1])
        cropped_image = image[ymin:ymax, xmin:xmax]
        return cropped_image


    def process_csv_file(csv_file_path, image_folder_path, output_folder_path):
        counter = 0
        with open(csv_file_path, 'r') as csv_file:
            next(csv_file)
            for csv_line in csv_file:
                parts = csv_line.strip().split(',')
                image_filename = parts[0]
                image_path = f"{image_folder_path}/{image_filename}"
                bbox = tuple(map(float, parts[5:9]))
                class_name = parts[1]
                shape_type = parts[2]
                occlusion_level = parts[3]
                orientation = parts[4]

                class_folder = os.path.join(output_folder_path, class_name)
                shape_folder = os.path.join(class_folder, shape_type)
                occlusion_folder = os.path.join(shape_folder, occlusion_level)
                orientation_folder = os.path.join(occlusion_folder, orientation)

                if not os.path.exists(class_folder):
                    os.makedirs(class_folder)
                if not os.path.exists(shape_folder):
                    os.makedirs(shape_folder)
                if not os.path.exists(occlusion_folder):
                    os.makedirs(occlusion_folder)
                if not os.path.exists(orientation_folder):
                    os.makedirs(orientation_folder)

                cropped_image = crop_bbox(image_path, bbox)
                filename, file_extension = os.path.splitext(image_filename)
                new_filename = f"{counter}{file_extension}"
                output_image_path = os.path.join(orientation_folder, new_filename)

                if cv2.imwrite(output_image_path, cropped_image):
                    print(f"Image saved successfully: {output_image_path} ({counter})")
                    counter += 1
                else:
                    print(f"Error saving image: {output_image_path}")


    # Example usage
    csv_file_path = "/path/to/your/csv/file"
    image_folder_path = "/path/to/your/image/folder"
    output_folder_path = "/path/to/your/output/folder"
    process_csv_file(csv_file_path, image_folder_path, output_folder_path)

## License
This project is licensed under the [MIT License](https://www.mit.edu/~amini/LICENSE.md).



