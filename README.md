# STEP 0: ACTIVATE THE ENVIRONMENT (Crucial!)
# Always run this first when opening a new terminal so Python knows where your installed tools are.
conda activate chat2svg

# STEP 1: CHOOSE YOUR NEW TARGET
# Decide on the name of the new object you want to generate (e.g., "ice_cream", "cat", "car"). 
# In this guide, we will use "ice_cream" as the example target.

# STEP 2: CREATE THE DIRECTORY
# Navigate to your output folder and create a dedicated directory for your new target.
cd ~/Chat2SVG_Project/Chat2SVG/output/example_generation/
mkdir ice_cream

# STEP 3: PROVIDE THE STARTING TEMPLATE
# The pipeline always needs a basic, uncolored starting shape to work with.
# You must place a basic SVG file inside your new folder and name it EXACTLY:
# ice_cream_template.svg
# 
# You can get this template in two ways:
# 1. Run the script in the "1_initialization" (or similar Stage 1) folder of your project to generate it via AI.
# 2. Manually draw or download a very basic SVG shape and upload it to that folder.

# STEP 4: RUN STAGE 2 (Image Generation & Path Segmentation)
# Once your ice_cream_template.svg is in the folder, go to Stage 2. 
# Remember to change the --target and --output_folder names to match your new object!
cd ~/Chat2SVG_Project/Chat2SVG/2_detail_enhancement
CUDA_VISIBLE_DEVICES=2 python main.py --target "ice_cream" --output_path "../output" --output_folder "example_generation/ice_cream" --num_inference_steps 20

# STEP 5: RUN STAGE 3 (Vector Optimization & Coloring)
# Finally, run the math engine to match the vector paths to the AI-generated image.
cd ~/Chat2SVG_Project/Chat2SVG/3_svg_optimization
CUDA_VISIBLE_DEVICES=2 python main.py --svg_folder "../output/example_generation/ice_cream" --target "ice_cream" --output_size 256 --smoothness_weight_img 2.0 --mse_loss_weight_img 2000.0 --kl_weight_img 0.2

# RESULT:
# Your completely custom, fully colored SVG will be saved as:
# ~/Chat2SVG_Project/Chat2SVG/output/example_generation/ice_cream/ice_cream_optim_point.svg




# TO KEEP RUNNING THE SERVER IN BACKGROUND EVEN AFTER DISCONNECTING ----
nohup bash -c 'cd ~/Chat2SVG_Project/Chat2SVG/2_detail_enhancement && PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True CUDA_VISIBLE_DEVICES=2 python main.py --target "ice_cream" --output_path "../output" --output_folder "example_generation/ice_cream" --num_inference_steps 20 && cd ~/Chat2SVG_Project/Chat2SVG/3_svg_optimization && CUDA_VISIBLE_DEVICES=2 python main.py --svg_folder "../output/example_generation/ice_cream" --target "ice_cream" --output_size 256 --smoothness_weight_img 2.0 --mse_loss_weight_img 2000.0 --kl_weight_img 0.2' > run_all.log 2>&1 &

# TO CHECK LIVE OUTPUT LOGS ----
tail -f run_all.log
[Ctrl-C to close]
